

```cs
[Route("api/odata/MyModuleName/v{version}/Example")]
public class ExampleController 
    : ODataController
{
    private readonly ISomeApplicationService _someService;

    public ExampleController(ISomeApplicationService someService)
    {
        _someService = someService;
    }

    [EnableQuery]
    [HttpGet("")]
    [HttpGet("$count")]
    //Applicable if you want WebAPI: [HttpGet("Get")]
    public IActionResult Get()
    {
        try {
        var x =  _someService.Get().AsQueryable();
        return Ok(x);
        }catch {
            ...
        }
    }

    [EnableQuery]
    [HttpGet("{id}")]
    public IActionResult Get(int id)
    {
        var result =  _someService.Get(id);
        if (result == null)
        {
            return NotFound(); // Returns a NotFoundResult
        }        
        return Ok(result);
    }

    // The async equivalent is:
    [EnableQuery]
    [HttpGet("{id}")]
    public async Task<IHttpActionResult> GetAsync(int id)
    {
        var result = await someService.GetAsync(id);

        if (result == null){
            //Generic solution, but no control over message:
            //return NotFound();
            //More specific (although prefer to use a custom extension method to keep code clean):
            var resp = new HttpResponseMessage(HttpStatusCode.NotFound)
            {
                Content = new StringContent(string.Format("No product with ID = {0}", id)),
                ReasonPhrase = "Product ID Not Found"
            };
            throw new HttpResponseException(resp);
        }


        return Ok(result);
    }
    // Post and Put are more like general Web API...
}
```


## Testing Examples ##

To test async Controller endpoints:
```cs
[Test]
public async Task GetProductAsyncReturnsCorrectProduct()
{
    var someServiceMock = GetMockService();
    var controller = new ExampleController(productDbMock);

    //Get IHttpActionResult:
    var result = await controller.GetProductAsync(123) as OkNegotiatedContentResult<Example>;

    Assert.IsNotNull(result);
    Assert.AreEqual(123, result.Content.Id);
}
```