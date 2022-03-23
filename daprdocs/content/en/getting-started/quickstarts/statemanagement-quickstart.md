---
type: docs
title: "Quickstart: State Management"
linkTitle: "State Management"
weight: 70
description: "Get started with Dapr's State Management building block"
---

Use [Dapr's State Management building block](https://docs.dapr.io/developing-applications/building-blocks/service-invocation) to store and query application data as key/value pairs in supported state stores.

<img src="/images/serviceinvocation-quickstart/service-invocation-overview.png" width=800 alt="Diagram showing the steps of state management" style="padding-bottom:25px;">

In this quickstart, you'll create a microservice to demonstrate Dapr's state management API. The example service below generates messages to store data in a state store.

Select your preferred language before proceeding with the quickstart.

{{< tabs "Python" "JavaScript" ".NET" "Java" "Go" >}}
 <!-- Python -->
{{% codetab %}}

### Pre-requisites

For this example, you will need:

- [Dapr CLI and initialized environment](https://docs.dapr.io/getting-started).
- [Python 3.7+ installed](https://www.python.org/downloads/).
<!-- IGNORE_LINKS -->
- [Docker Desktop](https://www.docker.com/products/docker-desktop)
<!-- END_IGNORE -->

### Step 1: Set up the environment

Clone the sample we've provided.

```bash
git clone https://github.com/dapr/quickstarts.git
```

### Step 2: Save, get, and delete a single state

In a terminal window, navigate to the `order-processor` directory.

```bash
cd state_management/python/sdk/order-processor
```

Install the dependencies:

```bash
pip3 install -r requirements.txt 
```

Run the `order-processor` service alongside a Dapr sidecar.

```bash
dapr run --app-id order-processor --components-path ../../../components/ -- python3 app.py
```

In the `order-processor` application, we're saving, receiving, and deleting the state via the [ `statestore.yaml` component)]({{< ref "#statestoreyaml-component-file" >}}). As soon as the service starts, it publishes 10 messages and then exits the application:

```python
DAPR_STORE_NAME = "statestore"
  with DaprClient() as client:

    # Save state into the state store
    client.save_state(DAPR_STORE_NAME, orderId, str(order))
    logging.info('Saving Order: %s', order)

    # Get state from the state store
    result = client.get_state(DAPR_STORE_NAME, orderId)
    logging.info('Result after get: ' + str(result.data))

    # Delete state from the state store
    client.delete_state(store_name=DAPR_STORE_NAME, key=orderId)
    logging.info('Deleting Order: %s', order)
```

### Step 4: View the output

Notice, as specified in the code above, the `order-processor` service saves, receives, and deletes a numbered message to the Dapr sidecar via the `statestore.yaml` component.

`order-processor` output:

```
== APP == INFO:root:Saving Order: {'orderId': '1'}
== APP == INFO:root:Result after get: b"{'orderId': '1'}"
== APP == INFO:root:Deleting Order: {'orderId': '1'}
== APP == INFO:root:Saving Order: {'orderId': '2'}
== APP == INFO:root:Result after get: b"{'orderId': '2'}"
== APP == INFO:root:Deleting Order: {'orderId': '2'}
== APP == INFO:root:Saving Order: {'orderId': '3'}
== APP == INFO:root:Result after get: b"{'orderId': '3'}"
== APP == INFO:root:Deleting Order: {'orderId': '3'}
== APP == INFO:root:Saving Order: {'orderId': '4'}
== APP == INFO:root:Result after get: b"{'orderId': '4'}"
== APP == INFO:root:Deleting Order: {'orderId': '4'}
== APP == INFO:root:Saving Order: {'orderId': '5'}
== APP == INFO:root:Result after get: b"{'orderId': '5'}"
== APP == INFO:root:Deleting Order: {'orderId': '5'}
== APP == INFO:root:Saving Order: {'orderId': '6'}
== APP == INFO:root:Result after get: b"{'orderId': '6'}"
== APP == INFO:root:Deleting Order: {'orderId': '6'}
== APP == INFO:root:Saving Order: {'orderId': '7'}
== APP == INFO:root:Result after get: b"{'orderId': '7'}"
== APP == INFO:root:Deleting Order: {'orderId': '7'}
== APP == INFO:root:Saving Order: {'orderId': '8'}
== APP == INFO:root:Result after get: b"{'orderId': '8'}"
== APP == INFO:root:Deleting Order: {'orderId': '8'}
== APP == INFO:root:Saving Order: {'orderId': '9'}
== APP == INFO:root:Result after get: b"{'orderId': '9'}"
== APP == INFO:root:Deleting Order: {'orderId': '9'}
```

#### `statestore.yaml` component file

When you run `dapr init`, Dapr creates a default Redis `statestore.yaml` and runs a Redis container on your local machine, located:

- On Windows, under `%UserProfile%\.dapr\components\statestore.yaml`
- On Linux/MacOS, under `~/.dapr/components/statestore.yaml`

With the `statestore.yaml` component, you can easily swap out underlying components without application code changes.

The Redis `statestore.yaml` file included for this quickstart contains the following:

```yaml
apiVersion: dapr.io/v1alpha1
kind: Component
metadata:
  name: statestore
spec:
  type: state.redis
  version: v1
  metadata:
  - name: redisHost
    value: localhost:6379
  - name: redisPassword
    value: ""
  - name: actorStateStore
    value: "true"
```

In the YAML file:

- `metadata/name` is how your application talks to the component.
- `spec/metadata` defines the connection to the instance of the component.
- `scopes` specify which application can use the component.

{{% /codetab %}}

 <!-- JavaScript -->
{{% codetab %}}

### Pre-requisites

For this example, you will need:

- [Dapr CLI and initialized environment](https://docs.dapr.io/getting-started).
- [Latest Node.js installed](https://nodejs.org/).
<!-- IGNORE_LINKS -->
- [Docker Desktop](https://www.docker.com/products/docker-desktop)
<!-- END_IGNORE -->

### Step 1: Set up the environment

Clone the sample we've provided.

```bash
git clone https://github.com/dapr/quickstarts.git
```

### Step 2: Save, get, and delete a single state

In a terminal window, navigate to the `order-processor` directory.

```bash
cd state_management/javascript/sdk/order-processor
```

Install the dependencies:

```bash
npm install 
```

Run the `order-processor` service alongside a Dapr sidecar.

```bash
dapr run --app-id order-processor --components-path ../../../components/ -- npm start
```

In the `order-processor` application, we're saving, receiving, and deleting the state via the [ `statestore.yaml` component)]({{< ref "#statestoreyaml-component-file" >}}). As soon as the service starts, it publishes 10 messages and then exits the application:

```javascript
const client = new DaprClient(DAPR_HOST, DAPR_HTTP_PORT);
const STATE_STORE_NAME = "statestore";

// Save state into the state store
client.state.save(STATE_STORE_NAME, [
    {
        key: orderId.toString(),
        value: order
    }
]);
console.log("Saving Order: ", order);

// Get state from the state store
var result = client.state.get(STATE_STORE_NAME, orderId.toString());
result.then(function(val) {
    console.log("Getting Order: ", val);
});

// Delete state from the state store
client.state.delete(STATE_STORE_NAME, orderId.toString());    
result.then(function(val) {
    console.log("Deleting Order: ", val);
});
```

### Step 4: View the output

Notice, as specified in the code above, the `order-processor` service saves, receives, and deletes a numbered message to the Dapr sidecar via the `statestore.yaml` component.

`order-processor` output:

```
== APP == Saving Order:  { orderId: 1 }
== APP == Saving Order:  { orderId: 2 }
== APP == Saving Order:  { orderId: 3 }
== APP == Saving Order:  { orderId: 4 }
== APP == Saving Order:  { orderId: 5 }
== APP == Saving Order:  { orderId: 6 }
== APP == Saving Order:  { orderId: 7 }
== APP == Saving Order:  { orderId: 8 }
== APP == Saving Order:  { orderId: 9 }
== APP == Saving Order:  { orderId: 10 }
== APP == Getting Order:  { orderId: 1 }
== APP == Getting Order:  { orderId: 2 }
== APP == Getting Order:  { orderId: 3 }
== APP == Getting Order:  { orderId: 4 }  
== APP == Getting Order:  { orderId: 5 }
== APP == Getting Order:  { orderId: 6 }
== APP == Getting Order:  { orderId: 7 }
== APP == Getting Order:  { orderId: 8 }
== APP == Getting Order:  { orderId: 9 }
== APP == Getting Order:  { orderId: 10 }
== APP == Deleting Order:  { orderId: 1 }
== APP == Deleting Order:  { orderId: 2 }
== APP == Deleting Order:  { orderId: 3 }
== APP == Deleting Order:  { orderId: 4 }
== APP == Deleting Order:  { orderId: 5 }
== APP == Deleting Order:  { orderId: 6 }
== APP == Deleting Order:  { orderId: 7 }
== APP == Deleting Order:  { orderId: 8 }
== APP == Deleting Order:  { orderId: 9 }
== APP == Deleting Order:  { orderId: 10 }
```

#### `statestore.yaml` component file

When you run `dapr init`, Dapr creates a default Redis `statestore.yaml` and runs a Redis container on your local machine, located:

- On Windows, under `%UserProfile%\.dapr\components\statestore.yaml`
- On Linux/MacOS, under `~/.dapr/components/statestore.yaml`

With the `statestore.yaml` component, you can easily swap out underlying components without application code changes.

The Redis `statestore.yaml` file included for this quickstart contains the following:

```yaml
apiVersion: dapr.io/v1alpha1
kind: Component
metadata:
  name: statestore
spec:
  type: state.redis
  version: v1
  metadata:
  - name: redisHost
    value: localhost:6379
  - name: redisPassword
    value: ""
  - name: actorStateStore
    value: "true"
```

In the YAML file:

- `metadata/name` is how your application talks to the component.
- `spec/metadata` defines the connection to the instance of the component.
- `scopes` specify which application can use the component.

{{% /codetab %}}

 <!-- .NET -->
{{% codetab %}}

### Pre-requisites

For this example, you will need:

- [Dapr CLI and initialized environment](https://docs.dapr.io/getting-started).
- [.NET SDK or .NET 6 SDK installed](https://dotnet.microsoft.com/download).
<!-- IGNORE_LINKS -->
- [Docker Desktop](https://www.docker.com/products/docker-desktop)
<!-- END_IGNORE -->

### Step 1: Set up the environment

Clone the sample we've provided.

```bash
git clone https://github.com/dapr/quickstarts.git
```

### Step 2: Save, get, and delete a single state

In a terminal window, navigate to the `order-processor` directory.

```bash
cd state_management/csharp/sdk/order-processor
```

Install the dependencies:

```bash
dotnet restore
dotnet build 
```

Run the `order-processor` service alongside a Dapr sidecar.

```bash
dapr run --app-id order-processor --components-path ../../../components/ -- dotnet run
```

In the `order-processor` application, we're saving, receiving, and deleting the state via the [ `statestore.yaml` component)]({{< ref "#statestoreyaml-component-file" >}}). As soon as the service starts, it publishes 10 messages and then exits the application:

```csharp
string DAPR_STORE_NAME = "statestore";
var client = new DaprClientBuilder().Build();

// Save state into the state store
await client.SaveStateAsync(DAPR_STORE_NAME, orderId.ToString(), order.ToString());
Console.WriteLine("Saving Order: " + order);

// Get state from the state store
var result = await client.GetStateAsync<string>(DAPR_STORE_NAME, orderId.ToString());
Console.WriteLine("Getting Order: " + result);
    
// Delete state from the state store
await client.DeleteStateAsync(DAPR_STORE_NAME, orderId.ToString());
Console.WriteLine("Deleting Order: " + order);
```

### Step 4: View the output

Notice, as specified in the code above, the `order-processor` service saves, receives, and deletes a numbered message to the Dapr sidecar via the `statestore.yaml` component.

`order-processor` output:

```
== APP == Saving Order: Order { orderId = 1 }
== APP == Getting Order: Order { orderId = 1 }
== APP == Deleting Order: Order { orderId = 1 }
== APP == Saving Order: Order { orderId = 2 }
== APP == Getting Order: Order { orderId = 2 }
== APP == Deleting Order: Order { orderId = 2 }
== APP == Saving Order: Order { orderId = 3 }
== APP == Getting Order: Order { orderId = 3 }
== APP == Deleting Order: Order { orderId = 3 }
== APP == Saving Order: Order { orderId = 4 }
== APP == Getting Order: Order { orderId = 4 }
== APP == Deleting Order: Order { orderId = 4 }
== APP == Saving Order: Order { orderId = 5 }
== APP == Getting Order: Order { orderId = 5 }
== APP == Deleting Order: Order { orderId = 5 }
== APP == Saving Order: Order { orderId = 6 }
== APP == Getting Order: Order { orderId = 6 }
== APP == Deleting Order: Order { orderId = 6 }
== APP == Saving Order: Order { orderId = 7 }
== APP == Getting Order: Order { orderId = 7 }
== APP == Deleting Order: Order { orderId = 7 }
== APP == Saving Order: Order { orderId = 8 }
== APP == Getting Order: Order { orderId = 8 }
== APP == Deleting Order: Order { orderId = 8 }
== APP == Saving Order: Order { orderId = 9 }
== APP == Getting Order: Order { orderId = 9 }
== APP == Deleting Order: Order { orderId = 9 }
== APP == Saving Order: Order { orderId = 10 }
== APP == Getting Order: Order { orderId = 10 }
== APP == Deleting Order: Order { orderId = 10 }
```

#### `statestore.yaml` component file

When you run `dapr init`, Dapr creates a default Redis `statestore.yaml` and runs a Redis container on your local machine, located:

- On Windows, under `%UserProfile%\.dapr\components\statestore.yaml`
- On Linux/MacOS, under `~/.dapr/components/statestore.yaml`

With the `statestore.yaml` component, you can easily swap out underlying components without application code changes.

The Redis `statestore.yaml` file included for this quickstart contains the following:

```yaml
apiVersion: dapr.io/v1alpha1
kind: Component
metadata:
  name: statestore
spec:
  type: state.redis
  version: v1
  metadata:
  - name: redisHost
    value: localhost:6379
  - name: redisPassword
    value: ""
  - name: actorStateStore
    value: "true"
```

In the YAML file:

- `metadata/name` is how your application talks to the component.
- `spec/metadata` defines the connection to the instance of the component.
- `scopes` specify which application can use the component.

{{% /codetab %}}

 <!-- Java -->
{{% codetab %}}

### Pre-requisites

For this example, you will need:

- [Dapr CLI and initialized environment](https://docs.dapr.io/getting-started).
- Java JDK 11 (or greater):
  - [Oracle JDK](https://www.oracle.com/technetwork/java/javase/downloads/index.html#JDK11), or
  - [OpenJDK](https://jdk.java.net/13/)
- [Apache Maven](https://maven.apache.org/install.html), version 3.x.
<!-- IGNORE_LINKS -->
- [Docker Desktop](https://www.docker.com/products/docker-desktop)
<!-- END_IGNORE -->

### Step 1: Set up the environment

Clone the sample we've provided.

```bash
git clone https://github.com/dapr/quickstarts.git
```

### Step 2: Save, get, and delete a single state

In a terminal window, navigate to the `order-processor` directory.

```bash
cd state_management/java/sdk/order-processor
```

Install the dependencies:

```bash
mvn clean install 
```

Run the `order-processor` service alongside a Dapr sidecar.

```bash
dapr run --app-id order-processor --components-path ../../../components/ -- java -jar target/order-processor-0.0.1-SNAPSHOT.jar
```

In the `order-processor` application, we're saving, receiving, and deleting the state via the [ `statestore.yaml` component)]({{< ref "#statestoreyaml-component-file" >}}). As soon as the service starts, it publishes 10 messages and then exits the application:

```java
private static final String DAPR_STATE_STORE = "statestore";
  try (DaprClient client = new DaprClientBuilder().build()) {
    // Save state into the state store
    client.saveState(DAPR_STATE_STORE, String.valueOf(orderId), order).block();
    LOGGER.info("Saving Order: " + order.getOrderId());

    // Get state from the state store
    State<Order> response = client.getState(DAPR_STATE_STORE, String.valueOf(orderId), Order.class).block();
    LOGGER.info("Getting Order: " + response.getValue().getOrderId());

    // Delete state from the state store
    client.deleteState(DAPR_STATE_STORE, String.valueOf(orderId)).block();
    LOGGER.info("Deleting Order: " + orderId);
    TimeUnit.MILLISECONDS.sleep(1000);
  }
```

### Step 4: View the output

Notice, as specified in the code above, the `order-processor` service saves, receives, and deletes a numbered message to the Dapr sidecar via the `statestore.yaml` component.

`order-processor` output:

```
== APP == 2014 [main] INFO com.service.OrderProcessingServiceApplication - Saving Order: 1
== APP == 2067 [main] INFO com.service.OrderProcessingServiceApplication - Getting Order: 1
== APP == 2075 [main] INFO com.service.OrderProcessingServiceApplication - Deleting Order: 1
== APP == 3098 [main] INFO com.service.OrderProcessingServiceApplication - Saving Order: 2
== APP == 3104 [main] INFO com.service.OrderProcessingServiceApplication - Getting Order: 2
== APP == 3109 [main] INFO com.service.OrderProcessingServiceApplication - Deleting Order: 2
== APP == 4114 [main] INFO com.service.OrderProcessingServiceApplication - Saving Order: 3
== APP == 4117 [main] INFO com.service.OrderProcessingServiceApplication - Getting Order: 3
== APP == 4120 [main] INFO com.service.OrderProcessingServiceApplication - Deleting Order: 3
== APP == 5128 [main] INFO com.service.OrderProcessingServiceApplication - Saving Order: 4
== APP == 5136 [main] INFO com.service.OrderProcessingServiceApplication - Getting Order: 4
== APP == 5141 [main] INFO com.service.OrderProcessingServiceApplication - Deleting Order: 4
== APP == 6153 [main] INFO com.service.OrderProcessingServiceApplication - Saving Order: 5
== APP == 6160 [main] INFO com.service.OrderProcessingServiceApplication - Getting Order: 5
== APP == 6167 [main] INFO com.service.OrderProcessingServiceApplication - Deleting Order: 5
== APP == 7175 [main] INFO com.service.OrderProcessingServiceApplication - Saving Order: 6
== APP == 7185 [main] INFO com.service.OrderProcessingServiceApplication - Getting Order: 6
== APP == 7190 [main] INFO com.service.OrderProcessingServiceApplication - Deleting Order: 6
== APP == 8196 [main] INFO com.service.OrderProcessingServiceApplication - Saving Order: 7
== APP == 8200 [main] INFO com.service.OrderProcessingServiceApplication - Getting Order: 7
== APP == 8203 [main] INFO com.service.OrderProcessingServiceApplication - Deleting Order: 7
== APP == 9221 [main] INFO com.service.OrderProcessingServiceApplication - Saving Order: 8
== APP == 9227 [main] INFO com.service.OrderProcessingServiceApplication - Getting Order: 8
== APP == 9233 [main] INFO com.service.OrderProcessingServiceApplication - Deleting Order: 8
== APP == 10241 [main] INFO com.service.OrderProcessingServiceApplication - Saving Order: 9
== APP == 10244 [main] INFO com.service.OrderProcessingServiceApplication - Getting Order: 9
== APP == 10247 [main] INFO com.service.OrderProcessingServiceApplication - Deleting Order: 9
== APP == 11268 [main] INFO com.service.OrderProcessingServiceApplication - Saving Order: 10
== APP == 11274 [main] INFO com.service.OrderProcessingServiceApplication - Getting Order: 10
== APP == 11278 [main] INFO com.service.OrderProcessingServiceApplication - Deleting Order: 10
```

#### `statestore.yaml` component file

When you run `dapr init`, Dapr creates a default Redis `statestore.yaml` and runs a Redis container on your local machine, located:

- On Windows, under `%UserProfile%\.dapr\components\statestore.yaml`
- On Linux/MacOS, under `~/.dapr/components/statestore.yaml`

With the `statestore.yaml` component, you can easily swap out underlying components without application code changes.

The Redis `statestore.yaml` file included for this quickstart contains the following:

```yaml
apiVersion: dapr.io/v1alpha1
kind: Component
metadata:
  name: statestore
spec:
  type: state.redis
  version: v1
  metadata:
  - name: redisHost
    value: localhost:6379
  - name: redisPassword
    value: ""
  - name: actorStateStore
    value: "true"
```

In the YAML file:

- `metadata/name` is how your application talks to the component.
- `spec/metadata` defines the connection to the instance of the component.
- `scopes` specify which application can use the component.

{{% /codetab %}}

 <!-- Go -->
{{% codetab %}}

### Pre-requisites

For this example, you will need:

- [Dapr CLI and initialized environment](https://docs.dapr.io/getting-started).
- [Latest version of Go](https://go.dev/dl/).
<!-- IGNORE_LINKS -->
- [Docker Desktop](https://www.docker.com/products/docker-desktop)
<!-- END_IGNORE -->

### Step 1: Set up the environment

Clone the sample we've provided.

```bash
git clone https://github.com/dapr/quickstarts.git
```

### Step 2: Save, get, and delete a single state

In a terminal window, navigate to the `order-processor` directory.

```bash
cd state_management/go/sdk/order-processor
```

Install the dependencies:

```bash
go build app.go 
```

Run the `order-processor` service alongside a Dapr sidecar.

```bash
dapr run --app-id order-processor --components-path ../../../components/ -- go run app.go
```

In the `order-processor` application, we're saving, receiving, and deleting the state via the [ `statestore.yaml` component)]({{< ref "#statestoreyaml-component-file" >}}). As soon as the service starts, it publishes 10 messages and then exits the application:

```go
client, err := dapr.NewClient()
STATE_STORE_NAME := "statestore"
// Save state into the state store
_ = client.SaveState(ctx, STATE_STORE_NAME, strconv.Itoa(orderId), []byte(order))
log.Print("Saving Order: " + string(order))

// Get state from the state store
result, _ := client.GetState(ctx, STATE_STORE_NAME, strconv.Itoa(orderId))
log.Print("Getting Order: " + string(result.Value))

// Delete state from the state store
_ = client.DeleteState(ctx, STATE_STORE_NAME, strconv.Itoa(orderId))
log.Print("Deleting Order: " + string(order))
```

### Step 4: View the output

Notice, as specified in the code above, the `order-processor` service saves, receives, and deletes a numbered message to the Dapr sidecar via the `statestore.yaml` component.

`order-processor` output:

```
== APP == dapr client initializing for: 127.0.0.1:50431
== APP == 2022/03/22 18:05:31 Saving Order: {"orderId":1}
== APP == 2022/03/22 18:05:31 Getting Order: {"orderId":1}
== APP == 2022/03/22 18:05:31 Deleting Order: {"orderId":1}
== APP == 2022/03/22 18:05:31 Saving Order: {"orderId":2}
== APP == 2022/03/22 18:05:31 Getting Order: {"orderId":2}
== APP == 2022/03/22 18:05:31 Deleting Order: {"orderId":2}
== APP == 2022/03/22 18:05:31 Saving Order: {"orderId":3}
== APP == 2022/03/22 18:05:31 Getting Order: {"orderId":3}
== APP == 2022/03/22 18:05:31 Deleting Order: {"orderId":3}
== APP == 2022/03/22 18:05:31 Saving Order: {"orderId":4}
== APP == 2022/03/22 18:05:31 Getting Order: {"orderId":4}
== APP == 2022/03/22 18:05:31 Deleting Order: {"orderId":4}
== APP == 2022/03/22 18:05:31 Saving Order: {"orderId":5}
== APP == 2022/03/22 18:05:31 Getting Order: {"orderId":5}
== APP == 2022/03/22 18:05:31 Deleting Order: {"orderId":5}
== APP == 2022/03/22 18:05:31 Saving Order: {"orderId":6}
== APP == 2022/03/22 18:05:31 Getting Order: {"orderId":6}
== APP == 2022/03/22 18:05:31 Deleting Order: {"orderId":6}
== APP == 2022/03/22 18:05:31 Saving Order: {"orderId":7}
== APP == 2022/03/22 18:05:31 Getting Order: {"orderId":7}
== APP == 2022/03/22 18:05:31 Deleting Order: {"orderId":7}
== APP == 2022/03/22 18:05:31 Saving Order: {"orderId":8}
== APP == 2022/03/22 18:05:31 Getting Order: {"orderId":8}
== APP == 2022/03/22 18:05:31 Deleting Order: {"orderId":8}
== APP == 2022/03/22 18:05:31 Saving Order: {"orderId":9}
== APP == 2022/03/22 18:05:31 Getting Order: {"orderId":9}
== APP == 2022/03/22 18:05:31 Deleting Order: {"orderId":9}
== APP == 2022/03/22 18:05:31 Saving Order: {"orderId":10}
== APP == 2022/03/22 18:05:31 Getting Order: {"orderId":10}
== APP == 2022/03/22 18:05:31 Deleting Order: {"orderId":10}
```

#### `statestore.yaml` component file

When you run `dapr init`, Dapr creates a default Redis `statestore.yaml` and runs a Redis container on your local machine, located:

- On Windows, under `%UserProfile%\.dapr\components\statestore.yaml`
- On Linux/MacOS, under `~/.dapr/components/statestore.yaml`

With the `statestore.yaml` component, you can easily swap out underlying components without application code changes.

The Redis `statestore.yaml` file included for this quickstart contains the following:

```yaml
apiVersion: dapr.io/v1alpha1
kind: Component
metadata:
  name: statestore
spec:
  type: state.redis
  version: v1
  metadata:
  - name: redisHost
    value: localhost:6379
  - name: redisPassword
    value: ""
  - name: actorStateStore
    value: "true"
```

In the YAML file:

- `metadata/name` is how your application talks to the component.
- `spec/metadata` defines the connection to the instance of the component.
- `scopes` specify which application can use the component.

{{% /codetab %}}

{{% /tabs %}}

## Tell us what you think!
We're continuously working to improve our quickstart examples and value your feedback. Did you find this quickstart helpful? Do you have suggestions for improvement?

Join the discussion in our [discord channel](https://discord.gg/22ZtJrNe).

## Next Steps

- Save, get, and delete stores using HTTP instead of an SDK.
  - [Python](https://github.com/dapr/quickstarts/tree/master/state_management/python/http)
  - [JavaScript](https://github.com/dapr/quickstarts/tree/master/state_management/javascript/http)
  - [.NET](https://github.com/dapr/quickstarts/tree/master/state_management/csharp/http)
  - [Java](https://github.com/dapr/quickstarts/tree/master/state_management/java/http)
  - [Go](https://github.com/dapr/quickstarts/tree/master/state_management/go/http)
- Learn more about [State Management building block]({{< ref state-management-overview.md >}})

{{< button text="Explore Dapr tutorials  >>" page="getting-started/tutorials/_index.md" >}}
