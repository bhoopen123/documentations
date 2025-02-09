# Intercept and change Microsoft.DataAnnotation validation message in .NET 8.0 Web Api
## Create a Web API Project
- The simple Post API to save a record
```csharp
    [ApiController]
    [Route("[controller]")]
    public class WeatherForecastController : ControllerBase
    {
        [HttpPost]
        public IActionResult Post(WeatherForecastRequest request)
        {
            // further logic for WeatherForecastRequest
            return Created();
        }
    }
```
- `WeatherForecastRequest` model will look like, 
```csharp
    public record WeatherForecastRequest([Required, StringLength(10)] string City,
                                         int Temperature,
                                         string? Summary)
    {

    }
```
-	Test Post API using Postman

![](/asp.net-web-api/imgs/PostmanResponse1.png)

-	Now adding one or more new json properties will not impact the API response,

![](/asp.net-web-api/imgs/PostmanResponse2.png)

## Add validation to restrict any unknown properties in API request

```csharp
builder.Services.AddControllers()
                .AddJsonOptions(options =>
                {
                    // this will restrict any unknown json properties by making the request 400 BadRequest
                    options.JsonSerializerOptions.UnmappedMemberHandling = JsonUnmappedMemberHandling.Disallow;
                });
```

-	Now the response will look like, 

![](/asp.net-web-api/imgs/PostmanResponse3.png)

Which is good, but there is a problem... the response is exposing C# model details i.e. `WebApi_CustomException.Models.WeatherForecstRequest`. We don’t want to expose such information in the response.

## Hide JsonException details in the response

This can be achieved in multiple ways,

### Approach 1: - By setting `AllowInputFormatterExceptionMessages `= false

```csharp
builder.Services.AddControllers()
                .AddJsonOptions(options =>
                {
                    // this will restrict any unknown json properties by making the request 400 BadRequest
                    options.JsonSerializerOptions.UnmappedMemberHandling = JsonUnmappedMemberHandling.Disallow;

                    // this will not allow JsonException details into the response
                    // and send a generic message `The input was not valid.`
                    options.AllowInputFormatterExceptionMessages = false;
                });
```

-	Response after making the above change, 

![](/asp.net-web-api/imgs/PostmanResponse4.png)

### Approach 2: - Using filter to change the message before sending the response

-	Create a new filter `ReformValidationMessageAttribute `,

```csharp
public class ReformValidationMessageAttribute : ActionFilterAttribute
	    {
	        public override void OnResultExecuting(ResultExecutingContext context)
	        {
	            if (context.Result is BadRequestObjectResult badRequestObjectResult)
	                if (badRequestObjectResult.Value is ValidationProblemDetails validationProblemDetails)
	                {
	                    foreach (var errorKeyValue in validationProblemDetails.Errors)
	                    {
	                        if (errorKeyValue.Key.StartsWith("$", StringComparison.Ordinal) && errorKeyValue.Value != null)
	                        {
	                            for (int i = 0; i < errorKeyValue.Value.Count(); i++)
	                            {
	                                if (errorKeyValue.Value[i].Contains("could not be mapped to any .NET member", StringComparison.Ordinal))
	                                {
	                                    errorKeyValue.Value[i] = "An unknown property was included in the request data.";
	                                }
	                            }
	                        }
	                    }
	                }
	
	            base.OnResultExecuting(context);
	        }
	    }
```

- This filter will check the response of any BadRequest and update the message containing internal C# details to the message we want to return.
- Add the filter to the controller options, 

```csharp
builder.Services.AddControllers(option =>
                {
                    option.Filters.Add(typeof(ReformValidationMessageAttribute));
                })
                .AddJsonOptions(options =>
                {
                    // this will restrict any unknown json properties by making the request 400 BadRequest
                    options.JsonSerializerOptions.UnmappedMemberHandling = JsonUnmappedMemberHandling.Disallow;
                });
```

![](/asp.net-web-api/imgs/PostmanResponse5.png)

