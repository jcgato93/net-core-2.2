# Paginación 

- Si tenemos , por ejemplo , un millón de recursos(registros),
no debemos retornarle ese millón de recursos a nuestros clientes,
pues esto sería muy lento. Para ello se utiliza la paginación

- La paginación nos permite segmentar un conjunto de recursos

- Cuando paginamos lo que hacemos es que de la base de datos solicitamos
unicamente el segmento de recurso que queremos obtener.

- El controlador debe tener por defecto dos valores, el
nuero de la pagina y la cantidad de registros, que deben venir desde un queryString

``` csharp
        // GET api/autores
        [HttpGet(Name = "ObtenerAutoresV1")]
        [ServiceFilter(typeof(HATEOASAuthorsFilterAttribute))]
        public async Task<ActionResult<IEnumerable<AutorDTO>>> Get(int numeroDePagina = 1, int cantidadDeRegistros = 10)
        {
            var query = context.Autores.AsQueryable();

            var totalDeRegistros = query.Count();

            var autores = await query
                .Skip(cantidadDeRegistros * (numeroDePagina - 1))
                .Take(cantidadDeRegistros)
                .ToListAsync();

            Response.Headers["X-Total-Registros"] = totalDeRegistros.ToString();

            // Celing aproxima el resultado hacia el numero mayor
            // es decir  7.03 a 8
            Response.Headers["X-Cantidad-Paginas"] = ((int)Math.Ceiling((double)totalDeRegistros / cantidadDeRegistros)).ToString();

            var autoresDTO = mapper.Map<List<AutorDTO>>(autores);
            return autoresDTO;
        }
```

- El metodo <strong>Skip</strong> indica cuantos registros debe saltarse,
el metodo <strong>Take</strong> indica la cantidad de registros que tomara 
de la consulta resultante. <strong>Celing</strong> aproxima el resultado hacia el numero mayor es decir  7.03 a 8.

- Tambien se envia como respuesta en la cabecera de la peticion(Headers) , el total de registros y la cantidad de paginas
segun los parametros que se recibieron en la peticion



# Forma avanzada de paginación

- Primero se crea una clase generica <T> para la paginacion

``` csharp
using System.Collections.Generic;

public class PaginatedItemsViewModel<TEntity> where TEntity : class
{
    public int PageIndex { get; private set; }

    public int PageSize { get; private set; }

    public long Count { get; private set; }

    public IEnumerable<TEntity> Data { get; private set; }

    public PaginatedItemsViewModel(int pageIndex, int pageSize, long count, IEnumerable<TEntity> data)
    {
        this.PageIndex = pageIndex;
        this.PageSize = pageSize;
        this.Count = count;
        this.Data = data;
    }
}
```


- Forma de paginacion en el controller , sin retornar el total de paginas

``` csharp
 // GET api/v1/[controller]/items[?pageSize=3&pageIndex=10]
[HttpGet]
[Route("items")]
[ProducesResponseType(typeof(PaginatedItemsViewModel<CatalogItem>), (int)HttpStatusCode.OK)]
[ProducesResponseType(typeof(IEnumerable<CatalogItem>), (int)HttpStatusCode.OK)]
[ProducesResponseType((int)HttpStatusCode.BadRequest)]
public async Task<IActionResult> ItemsAsync([FromQuery]int pageSize = 10, [FromQuery]int pageIndex = 0, string ids = null)
{
    if (!string.IsNullOrEmpty(ids))
    {
        var items = await GetItemsByIdsAsync(ids);

        if (!items.Any())
        {
            return BadRequest("ids value invalid. Must be comma-separated list of numbers");
        }

        return Ok(items);
    }

    var totalItems = await _catalogContext.CatalogItems
        .LongCountAsync();

    var itemsOnPage = await _catalogContext.CatalogItems
        .OrderBy(c => c.Name)
        .Skip(pageSize * pageIndex)
        .Take(pageSize)
        .ToListAsync();    

    var model = new PaginatedItemsViewModel<CatalogItem>(pageIndex, pageSize, totalItems, itemsOnPage);

    return Ok(model);
}
```