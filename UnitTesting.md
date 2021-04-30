# Unit Testing HttpClient Call

```csharp

using System;
using System.Net;
using System.Net.Http;
using System.Threading.Tasks;
using FluentAssertions;
using Microsoft.VisualStudio.TestTools.UnitTesting;
using Moq;
using Moq.Protected;

namespace UnitTesting
{
    // Using MS Unit Testing Tools here
    [TestClass]
    public class TestClient
    {
        [TestMethod]
        public async Task TestCall()
        {
            // Pre the handler and response
            var mockHttpMessageHandler = new Mock<HttpMessageHandler>();
            mockHttpMessageHandler.Protected()
                .Setup<Task<HttpResponseMessage>>(
                    "SendAsync",
                    ItExpr.IsAny<HttpRequestMessage>(),
                    ItExpr.IsAny<CancellationToken>()
                )
                .ReturnsAsync(GetFailResponse(DummyErrorCode));

            var httpClient = new HttpClient(mockHttpMessageHandler.Object);
            
            ServiceToTest service = new ServiceToTest(httpClient);
            string result = service.MethodToTest();  // Method that woudl return the content of the HTTP call.
            result.Should().Be("some name");
        }
        
        private HttpResponseMessage GetJsonResponse() {
            var httpResponse = new HttpResponseMessage {
                StatusCode = HttpStatusCode.OK,
                Content = new StringContent("{\"name\":\"some name\",\"id\":\"123\"}")
            };
            httpResponse.Headers.Add("some-guid-header", Guid.Empty.ToString());

            return httpResponse;
        }

    }
}
```
