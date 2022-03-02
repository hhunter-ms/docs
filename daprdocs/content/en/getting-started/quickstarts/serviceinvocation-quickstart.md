---
type: docs
title: "Quickstart: Service Invocation"
linkTitle: "Service Invocation"
weight: 70
description: "Get started with Dapr's Service Invocation building block"
---

With [Dapr's Service Invocation building block](https://docs.dapr.io/developing-applications/building-blocks/service-invocation), your application can communicate reliably and securely with other applications using standard [gRPC](https://grpc.io) or [HTTP](https://www.w3.org/Protocols/) protocols.

{{% alert title="Note" color="primary" %}}
 Dapr offers several ways to enable service invocation. While we recommend enabling with http proxy, you can explore all of Dapr's Service Invocation options in [our building blocks section]({{< ref "developing-applications/building-blocks/service-invocation/howto-invoke-discover-services" >}}).

{{% /alert %}}

Using sidecar architecture:

1. Dapr provides a service invocation API that acts as a combination of a reverse proxy with built-in service discovery. 
1. The sidecar programming model encourages each application to talk to its own Dapr instance. 
1. The Dapr instances discover and communicate with one another.

<img src="/images/service-invocation-overview.png" width=800 alt="Diagram showing the steps of service invocation">

In this quickstart, you'll enable Dapr's service invocation by creating:

- A checkout service, and
- An order processor service.

The checkout service will use Dapr's http proxying capability to invoke a method in the order processing service.

Select your preferred language before proceeding with the quickstart.

{{< tabs "Python" "JavaScript" ".NET" "Go" >}}
 <!-- Python -->
{{% codetab %}}

### Pre-requisites

For this example, you will need:

- [Dapr CLI and initialized environment](https://docs.dapr.io/getting-started).
- [Python 3.7+ installed](https://www.python.org/downloads/).
- [Docker Desktop](https://www.docker.com/products/docker-desktop).

### Step 1: Set up the environment

Clone the sample we've provided.

```bash
git clone https://github.com/dapr/quickstarts.git
```

### Run `checkout` service

In a terminal window, navigate to the `checkout` directory.

```bash
cd service_invocation/python/http/checkout
```

Install the dependencies:

```bash
pip3 install -r requirements.txt 
```

Run the `checkout` service alongside a Dapr sidecar.

```bash
dapr run  --app-id checkout --app-protocol http --dapr-http-port 3500 -- python3 app.py
```

```python
headers = {'dapr-app-id': 'order-processor'}

result = requests.post(
    url='%s/orders' % (base_url),
    data=json.dumps(order),
    headers=headers
)
```

### Run `order-processor` service

In a new terminal window, navigate to `order-processor` directory.

```bash
cd service_invocation/python/http/order-processor
```

Install the dependencies:

```bash
pip3 install -r requirements.txt 
```

Run the `order-processor` service alongside a Dapr sidecar.

```bash
dapr run --app-port 6001 --app-id order-processor --app-protocol http --dapr-http-port 3501 -- python3 app.py
```

```py
@app.route('/orders', methods=['POST'])
def getOrder():
    data = request.json
    print('Order received : ' + json.dumps(data), flush=True)
    return json.dumps({'success': True}), 200, {
        'ContentType': 'application/json'}


app.run(port=5001)
```

{{% /codetab %}}

 <!-- JavaScript -->
{{% codetab %}}

### Pre-requisites

For this example, you will need:

- [Dapr CLI and initialized environment](https://docs.dapr.io/getting-started).
- [Latest Node.js installed](https://nodejs.org/en/download/).
- [Docker Desktop](https://www.docker.com/products/docker-desktop).

### Step 1: Set up the environment

Clone the sample we've provided.

```bash
git clone https://github.com/dapr/quickstarts.git
```

### Run `checkout` service

In a terminal window, navigate to the `checkout` directory.

```bash
cd service_invocation/javascript/http/checkout
```

Install the dependencies:

```bash
npm install
```

Run the `checkout` service alongside a Dapr sidecar.

```bash
dapr run  --app-id checkout --app-protocol http --dapr-http-port 3500 -- npm start
```

In the `checkout` service, you'll notice there's no need to rewrite your app code to use Dapr's service invocation. You can enable service invocation by simply identifying the `dapr-app-id` header.

```javascript
let axiosConfig = {
  headers: {
      "dapr-app-id": "order-processor"
  }
};
  const res = await axios.post(`${DAPR_HOST}:${DAPR_HTTP_PORT}/orders`, order , axiosConfig);
  console.log("Order passed: " + res.config.data);
```

### Run `order-processor` service

In a new terminal window, navigate to `order-processor` directory.

```bash
cd service_invocation/javascript/http/order-processor
```

Install the dependencies:

```bash
npm install
```

Run the `order-processor` service alongside a Dapr sidecar.

```bash
dapr run --app-port 6001 --app-id order-processor --app-protocol http --dapr-http-port 3501 -- npm start
```

```javascript
app.post('/orders', (req, res) => {
    console.log("Order received:", req.body);
    res.sendStatus(200);
});
```

{{% /codetab %}}

 <!-- .NET -->
{{% codetab %}}

### Pre-requisites

For this example, you will need:

- [Dapr CLI and initialized environment](https://docs.dapr.io/getting-started).
- [.NET SDK or .NET 6 SDK installed](https://dotnet.microsoft.com/en-us/download).
- [Docker Desktop](https://www.docker.com/products/docker-desktop).

### Step 1: Set up the environment

Clone the sample we've provided.

```bash
git clone https://github.com/dapr/quickstarts.git
```

### Run `checkout` service

In a terminal window, navigate to the `checkout` directory.

```bash
cd service_invocation/csharp/http/checkout
```

Install the dependencies:

```bash
dotnet restore
dotnet build
```

Run the `checkout` service alongside a Dapr sidecar.

```bash
dapr run  --app-id checkout --app-protocol http --dapr-http-port 3500 -- npm start
```

```csharp
var client = new HttpClient();
client.DefaultRequestHeaders.Accept.Add(new System.Net.Http.Headers.MediaTypeWithQualityHeaderValue("application/json"));

client.DefaultRequestHeaders.Add("dapr-app-id", "order-processor");

 var response = await client.PostAsync($"{baseURL}/orders", content);
    Console.WriteLine("Order passed: " + order);
```

### Run `order-processor` service

In a new terminal window, navigate to `order-processor` directory.

```bash
cd service_invocation/javascript/http/order-processor
```

Install the dependencies:

```bash
npm install
```

Run the `order-processor` service alongside a Dapr sidecar.

```bash
dapr run --app-port 6001 --app-id order-processor --app-protocol http --dapr-http-port 3501 -- npm start
```

```csharp
app.MapPost("/orders", async context => {
    var data = await context.Request.ReadFromJsonAsync<Order>();
    Console.WriteLine("Order received : " + data);
    await context.Response.WriteAsync(data.ToString());
});
```

{{% /codetab %}}

 <!-- Go -->
{{% codetab %}}

### Pre-requisites

For this example, you will need:

- [Dapr CLI and initialized environment](https://docs.dapr.io/getting-started).
- [.NET SDK or .NET 6 SDK installed](https://dotnet.microsoft.com/en-us/download).
- [Docker Desktop](https://www.docker.com/products/docker-desktop).

### Step 1: Set up the environment

Clone the sample we've provided.

```bash
git clone https://github.com/dapr/quickstarts.git
```

### Run `checkout` service

In a terminal window, navigate to the `checkout` directory.

```bash
cd service_invocation/csharp/http/checkout
```

Install the dependencies:

```bash
go build app.go
```

Run the `checkout` service alongside a Dapr sidecar.

```bash
dapr run  --app-id checkout --app-protocol http --dapr-http-port 3500 -- go run app.go
```

```go
req.Header.Add("dapr-app-id", "order-processor")

response, err := client.Do(req)

if err != nil {
			fmt.Print(err.Error())
			os.Exit(1)
		}

		result, err := ioutil.ReadAll(response.Body)
		if err != nil {
			log.Fatal(err)
		}

		log.Println("Order passed: ", string(result))
```

### Run `order-processor` service

In a new terminal window, navigate to `order-processor` directory.

```bash
cd service_invocation/javascript/http/order-processor
```

Install the dependencies:

```bash
go build app.go
```

Run the `order-processor` service alongside a Dapr sidecar.

```bash
dapr run --app-port 6001 --app-id order-processor --app-protocol http --dapr-http-port 3501 -- go run app.go
```

```go
func getOrder(w http.ResponseWriter, r *http.Request) {
	data, err := ioutil.ReadAll(r.Body)
	if err != nil {
		log.Fatal(err)
	}
	log.Printf("Order received : %s", string(data))
```

{{% /codetab %}}

{{% /tabs %}}

