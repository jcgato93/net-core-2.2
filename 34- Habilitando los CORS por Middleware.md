# Habilitando CORS por Middleware

- En la calse Startup.cs en el metodo ConfigureServices


```csharp
public void ConfigureServices(IServiceCollection services){

    /**
    este debe ir antes de cualquier otro middleware
    **/
    services.AddCors(); 

    // ---- Otros middleware
}
```

- Configuracion de los tipos de CORS aceptados en el 
metodo Configure

```csharp
public void Configure(IApplicationBuilder app,IHostingEnviroment env){
    // otro codigo

    app.UseHttpsRedirection();
    app.UseAuthentication();

    //=== Se puede especificar de forma explicita sobre el citio que puede hacer solicitudes
    //app.UseCors(builder => builder.WithOrigins("https://www.test.com"));

     //=== Especificando origen y tipos de peticion permitidos
    //app.UseCors(builder => builder.WithOrigins("https://www.test.com").WithMethods("GET","POST").WithHeaders("*"));


    // Especificando cualquier origen con cualquier metodo de peticion
    app.UseCors(builder => builder.WithOrigins("*").WithMethods("*").WithHeaders("*"));

    // Especificando cualquier origen con cualquier metodo de peticion con los metodos del C#
    //app.UseCors(builder => builder.AllowAnyOrigin().AllowAnyMethod().AllowAnyHeader());

    app.UseMvc();
}
```

