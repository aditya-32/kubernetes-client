# Kubernetes & OpenShift Java Client [![Join the chat at https://gitter.im/fabric8io/kubernetes-client](https://badges.gitter.im/fabric8io/kubernetes-client.svg)](https://gitter.im/fabric8io/kubernetes-client?utm_source=badge&utm_medium=badge&utm_campaign=pr-badge&utm_content=badge)
This client provides access to the full [Kubernetes](http://kubernetes.io/) &
[OpenShift](http://openshift.org/) REST APIs via a fluent DSL.

[![Build](https://github.com/fabric8io/kubernetes-client/workflows/Build/badge.svg)](https://github.com/fabric8io/kubernetes-client/actions?query=workflow%3ABuild)
[![Sonar Scanner](https://github.com/fabric8io/kubernetes-client/workflows/Sonar%20Scanner/badge.svg)](https://github.com/fabric8io/kubernetes-client/actions?query=workflow%3A%22Sonar+Scanner%22)
[![E2E Tests](https://github.com/fabric8io/kubernetes-client/workflows/E2E%20Tests/badge.svg)](https://github.com/fabric8io/kubernetes-client/actions?query=workflow%3A%22E2E+Tests%22)
[![Release](https://img.shields.io/github/v/release/fabric8io/kubernetes-client)](https://search.maven.org/search?q=g:io.fabric8%20a:kubernetes-client)
[![Twitter](https://img.shields.io/twitter/follow/fabric8io?style=social)](https://twitter.com/fabric8io)
[![Bugs](https://sonarcloud.io/api/project_badges/measure?project=fabric8io_kubernetes-client&metric=bugs)](https://sonarcloud.io/dashboard?id=fabric8io_kubernetes-client)


* kubernetes-client: [![Maven Central](https://img.shields.io/maven-central/v/io.fabric8/kubernetes-client.svg?maxAge=2592000)](http://search.maven.org/#search%7Cga%7C1%7Cg%3Aio.fabric8%20a%3Akubernetes-client)
[![Javadocs](http://www.javadoc.io/badge/io.fabric8/kubernetes-client.svg?color=blue)](http://www.javadoc.io/doc/io.fabric8/kubernetes-client)
* kubernetes-model-core: [![Maven Central](https://img.shields.io/maven-central/v/io.fabric8/kubernetes-model-core.svg?maxAge=2592000)](http://search.maven.org/#search%7Cga%7C1%7Cg%3Aio.fabric8%20a%3Akubernetes-model-core)
[![Javadocs](http://www.javadoc.io/badge/io.fabric8/kubernetes-model-core.svg?color=blue)](http://www.javadoc.io/doc/io.fabric8/kubernetes-model-core)
* openshift-client: [![Maven Central](https://img.shields.io/maven-central/v/io.fabric8/openshift-client.svg?maxAge=2592000)](http://search.maven.org/#search%7Cga%7C1%7Cg%3Aio.fabric8%20a%3Aopenshift-client)
[![Javadocs](http://www.javadoc.io/badge/io.fabric8/openshift-client.svg?color=blue)](http://www.javadoc.io/doc/io.fabric8/openshift-client)
* knative-client: [![Maven Central](https://img.shields.io/maven-central/v/io.fabric8/knative-client.svg?maxAge=2592000)](http://search.maven.org/#search%7Cga%7C1%7Cg%3Aio.fabric8%20a%3Aknative-client)
[![Javadocs](http://www.javadoc.io/badge/io.fabric8/knative-client.svg?color=blue)](http://www.javadoc.io/doc/io.fabric8/knative-client)
* tekton-client: [![Maven Central](https://img.shields.io/maven-central/v/io.fabric8/tekton-client.svg?maxAge=2592000)](http://search.maven.org/#search%7Cga%7C1%7Cg%3Aio.fabric8%20a%3Atekton-client)
[![Javadocs](http://www.javadoc.io/badge/io.fabric8/tekton-client.svg?color=blue)](http://www.javadoc.io/doc/io.fabric8/tekton-client)
* servicecatalog-client: [![Maven Central](https://img.shields.io/maven-central/v/io.fabric8/servicecatalog-client.svg?maxAge=2592000)](http://search.maven.org/#search%7Cga%7C1%7Cg%3Aio.fabric8%20a%3Aservicecatalog-client)
[![Javadocs](http://www.javadoc.io/badge/io.fabric8/servicecatalog-client.svg?color=blue)](http://www.javadoc.io/doc/io.fabric8/servicecatalog-client)

- [Usage](#usage)
    - [Creating a client](#creating-a-client)
    - [Configuring the client](#configuring-the-client)
    - [Loading resources from external sources](#loading-resources-from-external-sources)
    - [Passing a reference of a resource to the client](#passing-a-reference-of-a-resource-to-the-client)
    - [Adapting a client](#adaptin-a-client)
        - [Adapting and close](#adapting-and-close)
- [Mocking Kubernetes](#mocking-kubernetes)
- [Who Uses Fabric8 Kubernetes Client?](#who-uses-kubernetes--openshift-java-client)
- [Kubernetes and Red Hat OpenShift Compatibility Matrix](#compatibility-matrix)
- [Kubernetes Client CHEAT SHEET](https://github.com/fabric8io/kubernetes-client/blob/master/doc/CHEATSHEET.md)

## Usage

### Creating a client
The easiest way to create a client is:

```java
KubernetesClient client = new DefaultKubernetesClient();
```

`DefaultOpenShiftClient` implements both the `KubernetesClient` & `OpenShiftClient` interface so if you need the
OpenShift extensions, such as `Build`s, etc then simply do:

```java
OpenShiftClient osClient = new DefaultOpenShiftClient();
```

### Configuring the client

This will use settings from different sources in the following order of priority:

* System properties
* Environment variables
* Kube config file
* Service account token & mounted CA certificate

System properties are preferred over environment variables. The following system properties & environment variables can be used for configuration:

| Property / Environment Variable | Description | Default value |
|---------------------------------|-------------|---------------|
| `kubernetes.disable.autoConfig` / `KUBERNETES_DISABLE_AUTOCONFIG` | Disable automatic configuration | `false` |
| `kubernetes.master` / `KUBERNETES_MASTER` | Kubernetes master URL | `https://kubernetes.default.svc` |
| `kubernetes.api.version` / `KUBERNETES_API_VERSION` | API version | `v1` |
| `openshift.url` / `OPENSHIFT_URL` | OpenShift master URL | Kubernetes master URL value |
| `kubernetes.oapi.version` / `KUBERNETES_OAPI_VERSION` | OpenShift API version | `v1` |
| `kubernetes.trust.certificates` / `KUBERNETES_TRUST_CERTIFICATES`| Trust all certificates | `false` |
| `kubernetes.disable.hostname.verification` / `KUBERNETES_DISABLE_HOSTNAME_VERIFICATION` | | `false` |
| `kubernetes.certs.ca.file` / `KUBERNETES_CERTS_CA_FILE` | | |
| `kubernetes.certs.ca.data` / `KUBERNETES_CERTS_CA_DATA` | | |
| `kubernetes.certs.client.file` / `KUBERNETES_CERTS_CLIENT_FILE` | | |
| `kubernetes.certs.client.data` / `KUBERNETES_CERTS_CLIENT_DATA` | | |
| `kubernetes.certs.client.key.file` / `KUBERNETES_CERTS_CLIENT_KEY_FILE` | | |
| `kubernetes.certs.client.key.data` / `KUBERNETES_CERTS_CLIENT_KEY_DATA` | | |
| `kubernetes.certs.client.key.algo` / `KUBERNETES_CERTS_CLIENT_KEY_ALGO` | Client key encryption algorithm | `RSA` |
| `kubernetes.certs.client.key.passphrase` / `KUBERNETES_CERTS_CLIENT_KEY_PASSPHRASE` | | |
| `kubernetes.auth.basic.username` / `KUBERNETES_AUTH_BASIC_USERNAME` | | |
| `kubernetes.auth.basic.password` / `KUBERNETES_AUTH_BASIC_PASSWORD` | | |
| `kubernetes.auth.tryKubeConfig` / `KUBERNETES_AUTH_TRYKUBECONFIG` | Configure client using Kubernetes config | `true` |
| `kubeconfig` / `KUBECONFIG` | Name of the kubernetes config file to read | `~/.kube/config` |
| `kubernetes.auth.tryServiceAccount` / `KUBERNETES_AUTH_TRYSERVICEACCOUNT` | Configure client from Service account | `true` |
| `kubernetes.tryNamespacePath` / `KUBERNETES_TRYNAMESPACEPATH` | Configure client namespace from Kubernetes service account namespace path | `true` |
| `kubernetes.auth.token` / `KUBERNETES_AUTH_TOKEN` | | |
| `kubernetes.watch.reconnectInterval` / `KUBERNETES_WATCH_RECONNECTINTERVAL` | Watch reconnect interval in ms | `1000` |
| `kubernetes.watch.reconnectLimit` / `KUBERNETES_WATCH_RECONNECTLIMIT` | Number of reconnect attempts (-1 for infinite) | `-1` |
| `kubernetes.connection.timeout` / `KUBERNETES_CONNECTION_TIMEOUT` | Connection timeout in ms (0 for no timeout) | `10000` |
| `kubernetes.request.timeout` / `KUBERNETES_REQUEST_TIMEOUT` | Read timeout in ms | `10000` |
| `kubernetes.rolling.timeout` / `KUBERNETES_ROLLING_TIMEOUT` | Rolling timeout in ms | `900000` |
| `kubernetes.logging.interval` / `KUBERNETES_LOGGING_INTERVAL` | Logging interval in ms | `20000` |
| `kubernetes.scale.timeout` / `KUBERNETES_SCALE_TIMEOUT` | Scale timeout in ms | `600000` |
| `kubernetes.websocket.timeout` / `KUBERNETES_WEBSOCKET_TIMEOUT` | Websocket timeout in ms | `5000` |
| `kubernetes.websocket.ping.interval` / `kubernetes_websocket_ping_interval` | Websocket ping interval in ms | `30000` |
| `kubernetes.max.concurrent.requests.per.host` / `KUBERNETES_MAX_CONCURRENT_REQUESTS_PER_HOST` | | `5` |
| `kubernetes.impersonate.username` / `KUBERNETES_IMPERSONATE_USERNAME` | `Impersonate-User` HTTP header value | |
| `kubernetes.impersonate.group` / `KUBERNETES_IMPERSONATE_GROUP` |  `Impersonate-Group` HTTP header value | |
| `kubernetes.tls.versions` / `KUBERNETES_TLS_VERSIONS` | TLS versions separated by `,` | `TLSv1.2` |
| `kubernetes.truststore.file` / `KUBERNETES_TRUSTSTORE_FILE` | | |
| `kubernetes.truststore.passphrase` / `KUBERNETES_TRUSTSTORE_PASSPHRASE` | | |
| `kubernetes.keystore.file` / `KUBERNETES_KEYSTORE_FILE` | | |
| `kubernetes.keystore.passphrase` / `KUBERNETES_KEYSTORE_PASSPHRASE` | | |

Alternatively you can use the `ConfigBuilder` to create a config object for the Kubernetes client:

```java
Config config = new ConfigBuilder().withMasterUrl("https://mymaster.com").build();
KubernetesClient client = new DefaultKubernetesClient(config);
```

###
Using the DSL is the same for all resources.

List resources:

```java
NamespaceList myNs = client.namespaces().list();

ServiceList myServices = client.services().list();

ServiceList myNsServices = client.services().inNamespace("default").list();
```

Get a resource:

```java
Namespace myns = client.namespaces().withName("myns").get();

Service myservice = client.services().inNamespace("default").withName("myservice").get();
```

Delete:

```java
Namespace myns = client.namespaces().withName("myns").delete();

Service myservice = client.services().inNamespace("default").withName("myservice").delete();
```

Editing resources uses the inline builders from the Kubernetes Model:

```java
Namespace myns = client.namespaces().withName("myns").edit()
                   .editMetadata()
                     .addToLabels("a", "label")
                   .endMetadata()
                   .done();

Service myservice = client.services().inNamespace("default").withName("myservice").edit()
                     .editMetadata()
                       .addToLabels("another", "label")
                     .endMetadata()
                     .done();
```

In the same spirit you can inline builders to create:

```java
Namespace myns = client.namespaces().createNew()
                   .withNewMetadata()
                     .withName("myns")
                     .addToLabels("a", "label")
                   .endMetadata()
                   .done();

Service myservice = client.services().inNamespace("default").createNew()
                     .withNewMetadata()
                       .withName("myservice")
                       .addToLabels("another", "label")
                     .endMetadata()
                     .done();
```

You can also set the apiVersion of the resource like in the case of SecurityContextConstraints :

```java
SecurityContextConstraints scc = new SecurityContextConstraintsBuilder()
		.withApiVersion("v1")
		.withNewMetadata().withName("scc").endMetadata()
		.withAllowPrivilegedContainer(true)
		.withNewRunAsUser()
		.withType("RunAsAny")
		.endRunAsUser()
		.build();
```

### Following events

Use `io.fabric8.kubernetes.api.model.Event` as T for Watcher:

```java
client.events().inAnyNamespace().watch(new Watcher<Event>() {

  @Override
  public void eventReceived(Action action, Event resource) {
    System.out.println("event " + action.name() + " " + resource.toString());
  }

  @Override
  public void onClose(KubernetesClientException cause) {
    System.out.println("Watcher close due to " + cause);
  }

});
```

### Working with extensions

The kubernetes API defines a bunch of extensions like `daemonSets`, `jobs`, `ingresses` and so forth which are all usable in the `extensions()` DSL:

e.g. to list the jobs...

```
jobs = client.batch().jobs().list();
```

### Loading resources from external sources

There are cases where you want to read a resource from an external source, rather than defining it using the clients DSL.
For those cases the client allows you to load the resource from:

- A file *(Supports both java.io.File and java.lang.String)*
- A url
- An input stream

Once the resource is loaded, you can treat it as you would, had you created it yourself.

For example lets read a pod, from a yml file and work with it:

    Pod refreshed = client.load('/path/to/a/pod.yml').fromServer().get();
    Boolean deleted = client.load('/workspace/pod.yml').delete();
    LogWatch handle = client.load('/workspace/pod.yml').watchLog(System.out);

### Passing a reference of a resource to the client

In the same spirit you can use an object created externally (either a reference or using its string representation).

For example:

    Pod pod = someThirdPartyCodeThatCreatesAPod();
    Boolean deleted = client.resource(pod).delete();

### Adapting the client

The client supports plug-able adapters. An example adapter is the [OpenShift Adapter](openshift-client/src/main/java/io/fabric8/openshift/client/OpenShiftExtensionAdapter.java)
which allows adapting an existing [KubernetesClient](kubernetes-client/src/main/java/io/fabric8/kubernetes/client/KubernetesClient.java) instance to an [OpenShiftClient](openshift-client/src/main/java/io/fabric8/openshift/client/OpenShiftClient.java) one.

 For example:

```java
KubernetesClient client = new DefaultKubernetesClient();

OpenShiftClient oClient = client.adapt(OpenShiftClient.class);
```

The client also support the isAdaptable() method which checks if the adaptation is possible and returns true if it does.

```java
KubernetesClient client = new DefaultKubernetesClient();
if (client.isAdaptable(OpenShiftClient.class)) {
    OpenShiftClient oClient = client.adapt(OpenShiftClient.class);
} else {
    throw new Exception("Adapting to OpenShiftClient not support. Check if adapter is present, and that env provides /oapi root path.");
}
```

#### Adapting and close
Note that when using adapt() both the adaptee and the target will share the same resources (underlying http client, thread pools etc).
This means that close() is not required to be used on every single instance created via adapt.
Calling close() on any of the adapt() managed instances or the original instance, will properly clean up all the resources and thus none of the instances will be usable any longer.


## Mocking Kubernetes

Along with the client this project also provides a kubernetes mock server that you can use for testing purposes.
The mock server is based on `https://github.com/square/okhttp/tree/master/mockwebserver` but is empowered by the DSL and features provided by `https://github.com/fabric8io/mockwebserver`.

The Mock Web Server has two modes of operation:

- Expectations mode
- CRUD mode

### Expectations mode

It's the typical mode where you first set which are the expected http requests and which should be the responses for each request.
More details on usage can be found at: https://github.com/fabric8io/mockwebserver

This mode has been extensively used for testing the client itself. Make sure you check [kubernetes-test](kubernetes-tests/src/test/java/io/fabric8/kubernetes/client/mock).

To add a Kubernetes server to your test:

     @Rule
     public KubernetesServer server = new KubernetesServer();

### CRUD mode

Defining every single request and response can become tiresome. Given that in most cases the mock webserver is used to perform simple crud based operations, a crud mode has been added.
When using the crud mode, the mock web server will store, read, update and delete kubernetes resources using an in memory map and will appear as a real api server.

To add a Kubernetes Server in crud mode to your test:

     @Rule
     public KubernetesServer server = new KubernetesServer(true, true);

Then you can use the server like:

    @Test
    public void testInCrudMode() {
    KubernetesClient client = server.getClient();
    final CountDownLatch deleteLatch = new CountDownLatch(1);
    final CountDownLatch closeLatch = new CountDownLatch(1);

      //CREATE
      client.pods().inNamespace("ns1").create(new PodBuilder().withNewMetadata().withName("pod1").endMetadata().build());

      //READ
      podList = client.pods().inNamespace("ns1").list();
      assertNotNull(podList);
      assertEquals(1, podList.getItems().size());

      //WATCH
      Watch watch = client.pods().inNamespace("ns1").withName("pod1").watch(new Watcher<Pod>() {
          @Override
          public void eventReceived(Action action, Pod resource) {
            switch (action) {
              case DELETED:
                deleteLatch.countDown();
                break;
              default:
                throw new AssertionFailedError(action.toString().concat(" isn't recognised."));
            }
          }
      
          @Override
          public void onClose(KubernetesClientException cause) {
            closeLatch.countDown();
          }
      });

      //DELETE
      client.pods().inNamespace("ns1").withName("pod1").delete();

      //READ AGAIN
      podList = client.pods().inNamespace("ns1").list();
      assertNotNull(podList);
      assertEquals(0, podList.getItems().size());

      assertTrue(deleteLatch.await(1, TimeUnit.MINUTES));
      watch.close();
      assertTrue(closeLatch.await(1, TimeUnit.MINUTES));
    }

### JUnit5 support through extension

You can use KubernetesClient mocking mechanism with JUnit5. Since it doesn't support `@Rule` and `@ClassRule` there is dedicated annotation `@EnableKubernetesMockClient`.
If you would like to create instance of mocked `KubernetesClient` for each test (JUnit4 `@Rule`) you need to declare instance of `KubernetesClient` as shown below.

    @EnableKubernetesMockClient
    class ExampleTest {
    
        KubernetesClient client;
    
        @Test
        public void testInStandardMode() {
            ...
        }
    }

In case you would like to define static instance of mocked server per all the test (JUnit4 `@ClassRule`) you need to declare instance of `KubernetesClient` as shown below.
You can also enable crudMode by using annotation field `crud`.

    @EnableKubernetesMockClient(crud = true)
    class ExampleTest {
    
        static KubernetesClient client;
    
        @Test
        public void testInCrudMode() {
            ...
        }
    }
    
## Compatibility Matrix

|                           | Kubernetes 1.4.9 | Kubernetes 1.6.0 | Kubernetes 1.7.0  | Kubernetes 1.9.0  | Kubernetes 1.10.0 | Kubernetes 1.11.0 | Kubernetes 1.12.0 | Kubernetes 1.14.2 | Kubernetes 1.15.3 | Kubernetes 1.17.0 | Kubernetes 1.18.0 |
|---------------------------|------------------|------------------|-------------------|-------------------|-------------------|-------------------|-------------------|-------------------|-------------------|-------------------|-------------------|
| kubernetes-client 1.3.92  | +                | +                | -                 | -                 | -                 | -                 | -                 | -                 | -                 | -                 | -                 |
| kubernetes-client 3.0.3   | -                | -                | ✓                 | -                 | -                 | -                 | -                 | -                 | -                 | -                 | -                 |
| kubernetes-client 3.0.10  | -                | ✓                | ✓                 | ✓                 | -                 | -                 | -                 | -                 | -                 | -                 | -                 |
| kubernetes-client 3.0.11  | -                | ✓                | ✓                 | ✓                 | -                 | -                 | -                 | -                 | -                 | -                 | -                 |
| kubernetes-client 3.1.12  | -                | ✓                | ✓                 | ✓                 | -                 | -                 | -                 | -                 | -                 | -                 | -                 |
| kubernetes-client 3.2.0   | -                | ✓                | ✓                 | ✓                 | -                 | -                 | -                 | -                 | -                 | -                 | -                 |
| kubernetes-client 4.0.0   | -                | ✓                | ✓                 | ✓                 | -                 | -                 | -                 | -                 | -                 | -                 | -                 |
| kubernetes-client 4.1.0   | -                | ✓                | ✓                 | ✓                 | -                 | -                 | -                 | -                 | -                 | -                 | -                 |
| kubernetes-client 4.1.1   | -                | -                | -                 | ✓                 | ✓                 | ✓                 | ✓                 | -                 | -                 | -                 | -                 |
| kubernetes-client 4.1.2   | -                | -                | -                 | ✓                 | ✓                 | ✓                 | ✓                 | -                 | -                 | -                 | -                 |
| kubernetes-client 4.1.3   | -                | -                | -                 | ✓                 | ✓                 | ✓                 | ✓                 | -                 | -                 | -                 | -                 |
| kubernetes-client 4.2.0   | -                | -                | -                 | ✓                 | ✓                 | ✓                 | ✓                 | -                 | -                 | -                 | -                 |
| kubernetes-client 4.2.1   | -                | -                | -                 | ✓                 | ✓                 | ✓                 | ✓                 | -                 | -                 | -                 | -                 |
| kubernetes-client 4.2.2   | -                | -                | -                 | ✓                 | ✓                 | ✓                 | ✓                 | -                 | -                 | -                 | -                 |
| kubernetes-client 4.3.0   | -                | -                | -                 | ✓                 | ✓                 | ✓                 | ✓                 | ✓                 | -                 | -                 | -                 |
| kubernetes-client 4.3.1   | -                | -                | -                 | ✓                 | ✓                 | ✓                 | ✓                 | ✓                 | -                 | -                 | -                 |
| kubernetes-client 4.4.0   | -                | -                | -                 | ✓                 | ✓                 | ✓                 | ✓                 | ✓                 | -                 | -                 | -                 |
| kubernetes-client 4.4.1   | -                | -                | -                 | ✓                 | ✓                 | ✓                 | ✓                 | ✓                 | -                 | -                 | -                 |
| kubernetes-client 4.4.2   | -                | -                | -                 | ✓                 | ✓                 | ✓                 | ✓                 | ✓                 | -                 | -                 | -                 |
| kubernetes-client 4.5.0   | -                | -                | -                 | ✓                 | ✓                 | ✓                 | ✓                 | ✓                 | -                 | -                 | -                 |
| kubernetes-client 4.5.1   | -                | -                | -                 | ✓                 | ✓                 | ✓                 | ✓                 | ✓                 | -                 | -                 | -                 |
| kubernetes-client 4.5.2   | -                | -                | -                 | ✓                 | ✓                 | ✓                 | ✓                 | ✓                 | -                 | -                 | -                 |
| kubernetes-client 4.6.0   | -                | -                | -                 | ✓                 | ✓                 | ✓                 | ✓                 | ✓                 | ✓                 | -                 | -                 |
| kubernetes-client 4.6.1   | -                | -                | -                 | ✓                 | ✓                 | ✓                 | ✓                 | ✓                 | ✓                 | -                 | -                 |
| kubernetes-client 4.6.2   | -                | -                | -                 | ✓                 | ✓                 | ✓                 | ✓                 | ✓                 | ✓                 | -                 | -                 |
| kubernetes-client 4.6.3   | -                | -                | -                 | ✓                 | ✓                 | ✓                 | ✓                 | ✓                 | ✓                 | -                 | -                 |
| kubernetes-client 4.6.4   | -                | -                | -                 | ✓                 | ✓                 | ✓                 | ✓                 | ✓                 | ✓                 | -                 | -                 |
| kubernetes-client 4.7.0   | -                | -                | -                 | ✓                 | ✓                 | ✓                 | ✓                 | ✓                 | ✓                 | ✓                 | -                 |
| kubernetes-client 4.7.1   | -                | -                | -                 | ✓                 | ✓                 | ✓                 | ✓                 | ✓                 | ✓                 | ✓                 | -                 |
| kubernetes-client 4.8.0   | -                | -                | -                 | ✓                 | ✓                 | ✓                 | ✓                 | ✓                 | ✓                 | ✓                 | -                 |
| kubernetes-client 4.9.0   | -                | -                | -                 | ✓                 | ✓                 | ✓                 | ✓                 | ✓                 | ✓                 | ✓                 | -                 |
| kubernetes-client 4.9.1   | -                | -                | -                 | ✓                 | ✓                 | ✓                 | ✓                 | ✓                 | ✓                 | ✓                 | -                 |
| kubernetes-client 4.9.2   | -                | -                | -                 | ✓                 | ✓                 | ✓                 | ✓                 | ✓                 | ✓                 | ✓                 | -                 |
| kubernetes-client 4.10.0  | -                | -                | -                 | ✓                 | ✓                 | ✓                 | ✓                 | ✓                 | ✓                 | ✓                 | ✓                 |
| kubernetes-client 4.10.1  | -                | -                | -                 | ✓                 | ✓                 | ✓                 | ✓                 | ✓                 | ✓                 | ✓                 | ✓                 |
| kubernetes-client 4.10.2  | -                | -                | -                 | ✓                 | ✓                 | ✓                 | ✓                 | ✓                 | ✓                 | ✓                 | ✓                 |
| kubernetes-client 4.10.3  | -                | -                | -                 | ✓                 | ✓                 | ✓                 | ✓                 | ✓                 | ✓                 | ✓                 | ✓                 |
| kubernetes-client 4.11.0  | -                | -                | -                 | ✓                 | ✓                 | ✓                 | ✓                 | ✓                 | ✓                 | ✓                 | ✓                 |
| kubernetes-client 4.11.1  | -                | -                | -                 | ✓                 | ✓                 | ✓                 | ✓                 | ✓                 | ✓                 | ✓                 | ✓                 |
| kubernetes-client 4.12.0  | -                | -                | -                 | ✓                 | ✓                 | ✓                 | ✓                 | ✓                 | ✓                 | ✓                 | ✓                 |

|                           | OpenShift  3.6.0 | OpenShift  3.7.0  | OpenShift  3.9.0  | OpenShift 3.10.0 | OpenShift 3.11.0 | OpenShift 4.1.0 | OpenShift 4.2.0 |
|---------------------------|------------------|-------------------|-------------------|------------------|------------------|-----------------|-----------------|
| openshift-client 1.3.92   | +                | -                 | -                 | -                | -                | -                | -              |
| openshift-client 3.0.3    | -                | ✓                 | -                 | -                | -                | -                | -              |
| openshift-client 3.0.10   | ✓                | ✓                 | ✓                 | -                | -                | -                | -              |
| openshift-client 3.0.11   | ✓                | ✓                 | ✓                 | -                | -                | -                | -              |
| openshift-client 3.1.12   | ✓                | ✓                 | ✓                 | -                | -                | -                | -              |
| openshift-client 3.2.0    | ✓                | ✓                 | ✓                 | -                | -                | -                | -              |
| openshift-client 4.0.0    | ✓                | ✓                 | ✓                 | -                | -                | -                | -              |
| openshift-client 4.1.0    | -                | ✓                 | ✓                 | ✓                | -                | -                | -              |
| openshift-client 4.1.1    | -                | -                 | ✓                 | ✓                | ✓                | -                | -              |
| openshift-client 4.1.2    | -                | -                 | ✓                 | ✓                | ✓                | -                | -              |
| openshift-client 4.1.3    | -                | -                 | ✓                 | ✓                | ✓                | -                | -              |
| openshift-client 4.2.0    | -                | -                 | ✓                 | ✓                | ✓                | -                | -              |
| openshift-client 4.2.1    | -                | -                 | ✓                 | ✓                | ✓                | -                | -              |
| openshift-client 4.2.2    | -                | -                 | ✓                 | ✓                | ✓                | -                | -              |
| openshift-client 4.3.0    | -                | -                 | ✓                 | ✓                | ✓                | -                | -              |
| openshift-client 4.3.1    | -                | -                 | ✓                 | ✓                | ✓                | -                | -              |
| openshift-client 4.4.0    | -                | -                 | ✓                 | ✓                | ✓                | ✓                | -              |
| openshift-client 4.4.1    | -                | -                 | ✓                 | ✓                | ✓                | ✓                | -              |
| openshift-client 4.4.2    | -                | -                 | ✓                 | ✓                | ✓                | ✓                | -              |
| openshift-client 4.5.0    | -                | -                 | ✓                 | ✓                | ✓                | ✓                | -              |
| openshift-client 4.5.1    | -                | -                 | ✓                 | ✓                | ✓                | ✓                | -              |
| openshift-client 4.5.2    | -                | -                 | ✓                 | ✓                | ✓                | ✓                | -              |
| openshift-client 4.6.0    | -                | -                 | ✓                 | ✓                | ✓                | ✓                | -              |
| openshift-client 4.6.1    | -                | -                 | ✓                 | ✓                | ✓                | ✓                | -              |
| openshift-client 4.6.2    | -                | -                 | ✓                 | ✓                | ✓                | ✓                | -              |
| openshift-client 4.6.3    | -                | -                 | ✓                 | ✓                | ✓                | ✓                | -              |
| openshift-client 4.6.4    | -                | -                 | ✓                 | ✓                | ✓                | ✓                | -              |
| openshift-client 4.7.0    | -                | -                 | ✓                 | ✓                | ✓                | ✓                | ✓              |
| openshift-client 4.7.1    | -                | -                 | ✓                 | ✓                | ✓                | ✓                | ✓              |
| openshift-client 4.8.0    | -                | -                 | ✓                 | ✓                | ✓                | ✓                | ✓              |
| openshift-client 4.9.0    | -                | -                 | ✓                 | ✓                | ✓                | ✓                | ✓              |
| openshift-client 4.9.1    | -                | -                 | ✓                 | ✓                | ✓                | ✓                | ✓              |
| openshift-client 4.9.2    | -                | -                 | ✓                 | ✓                | ✓                | ✓                | ✓              |
| openshift-client 4.10.0   | -                | -                 | ✓                 | ✓                | ✓                | ✓                | ✓              |
| openshift-client 4.10.1   | -                | -                 | ✓                 | ✓                | ✓                | ✓                | ✓              |
| openshift-client 4.10.2   | -                | -                 | ✓                 | ✓                | ✓                | ✓                | ✓              |
| openshift-client 4.10.3   | -                | -                 | ✓                 | ✓                | ✓                | ✓                | ✓              |
| openshift-client 4.11.0   | -                | -                 | ✓                 | ✓                | ✓                | ✓                | ✓              |
| openshift-client 4.11.1   | -                | -                 | ✓                 | ✓                | ✓                | ✓                | ✓              |
| openshift-client 4.12.0   | -                | -                 | ✓                 | ✓                | ✓                | ✓                | ✓              |

## Major Changes in Kubernetes Client 4.0.0
All the resource objects used here will be according to OpenShift 3.9.0  and Kubernetes 1.9.0. All the resource objects will give all the fields according to OpenShift 3.9.0 and Kubernetes 1.9.0

* SecurityContextConstraints has been moved to OpenShift client from Kubernetes Client
* Job dsl is in both `batch` and `extensions`(Extensions is deprecated)
* DaemonSet dsl is in both `apps` and `extensions`(Extensions is deprecated)
* Deployment dsl is in both `apps` and `extensions`(Extensions is deprecated)
* ReplicaSet dsl is in both `apps` and `extensions`(Extensions is deprecated)
* NetworkPolicy dsl is in both `network` and `extensions`(Extensions is deprecated)
* Storage Class moved from `client base DSL` to `storage` DSL
* PodSecurityPolicies moved from `client base DSL` and `extensions` to only `extensions`
* ThirdPartyResource has been removed.

## Who uses Kubernetes & OpenShift Java client? 
  
  Extensions:
  - [Istio API](https://github.com/snowdrop/istio-java-api)
  - [Service Catalog API](https://github.com/fabric8io/kubernetes-client/tree/master/extensions/service-catalog)
  - [Knative](https://github.com/fabric8io/kubernetes-client/tree/master/extensions/knative)
  - [Tekton](https://github.com/fabric8io/kubernetes-client/tree/master/extensions/tekton)
  
  Frameworks/Libraries/Tools:
  - [Arquillian Cube](http://arquillian.org/arquillian-cube/)
  - [Apache Camel](https://github.com/apache/camel/blob/master/README.md)
  - [Apache Spark](https://github.com/apache/spark/tree/master/resource-managers/kubernetes)
  - [Jaeger Kubernetes](https://github.com/jaegertracing/jaeger-kubernetes)
  - [Loom](https://github.com/datawire/loom)
  - [Microsoft Azure Libraries for Java](https://github.com/Azure/azure-libraries-for-java)
  - [Spinnaker Halyard](https://github.com/spinnaker/halyard) 
  - [Spring Cloud Connectors for Kubernetes](https://github.com/spring-cloud/spring-cloud-kubernetes-connector)  
  - [Spring Cloud Kubernetes](https://github.com/fabric8io/spring-cloud-kubernetes)

  CI Plugins:
  - [Deployment Pipeline Plugin (Jenkins)](https://github.com/pearsontechnology/deployment-pipeline-jenkins-plugin) 
  - [Kubernetes Eleastic Agent (GoCD)](https://github.com/gocd/kubernetes-elastic-agents)
  - [Kubernetes Plugin (Jenkins)](https://github.com/jenkinsci/kubernetes-plugin)
  - [Kubernetes Pipeline Plugin (Jenkins)](https://github.com/jenkinsci/kubernetes-pipeline-plugin)
  - [OpenShift Sync Plugin (Jenkins)](https://github.com/openshift/jenkins-sync-plugin)
  - [Kubernetes Plugin (Teamcity from Jetbrains)](https://github.com/JetBrains/teamcity-kubernetes-plugin)
  - [Kubernetes Agents for Bamboo (WindTunnel Technologies)](https://marketplace.atlassian.com/apps/1222674/kubernetes-agents-for-bamboo)
  
  Build Tools:
  - [Fabric8 Maven Plugin](https://github.com/fabric8io/fabric8-maven-plugin)
  - [Eclipse JKube](https://github.com/eclipse/jkube)
  - [Gradle Kubernetes Plugin](https://github.com/bmuschko/gradle-kubernetes-plugin)
  
  Platforms:
  - [Apache Openwhisk](https://github.com/apache/incubator-openwhisk)
  - [Eclipse che](https://www.eclipse.org/che/)
  - [EnMasse](https://enmasse.io)
  - [Openshift.io (Launcher service)](https://github.com/fabric8-launcher)
  - [Spotify Styx](https://github.com/spotify/styx)
  - [Strimzi](https://github.com/strimzi/)
  - [Syndesis](https://syndesis.io/)
  
  Proprietary Platforms:
  - [vCommander](https://www.embotics.com/hybrid-cloud-management-platform)
  
As our community grows, we would like to track keep track of our users. Please send a PR with your organization/community name.   

## Tests we run for every new Pull Request
There are the links of the CircleCI and Jenkins for the tests which run for every new Pull Request. You can view all the recent builds also.

* [Regression Test](https://circleci.com/gh/fabric8io/kubernetes-client)
* [Unit Test](https://ci.fabric8.io/job/kubernetes-client-pullreq/)

To get the updates about the releases, you can join https://groups.google.com/forum/embed/?place=forum/fabric8-devclients
