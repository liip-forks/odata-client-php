# Usage Examples

## Initialize the OData Client

```php
    use SaintSystems\OData;

    // Using the example OData service from odata.org
    $odataServiceUrl = 'http://services.odata.org/V4/TripPinService';
    $odataClient = new ODataClient($odataServiceUrl);
```

## Initialize the OData Client with an Authentication Delegate

```php
    use SaintSystems\OData;

    // Need to authenticate the requests to the service? 
    // Pass in an Authentication Delegate Closure as the second argument
    // which will get passed the request object. The Closure will be
    // called prior to every request.

    // Access token retrieved from an oauth provider
    $accessToken = 'abc';
    $odataClient = new ODataClient($odataServiceUrl, function($request) {

        // OAuth Bearer Token Authentication
        $request->headers['Authorization'] = 'Bearer '.$accessToken;

        // OR Basic Authentication
        $username = 'foo';
        $password = 'bar';
        $request->headers['Authorization'] = 'Basic '.base64_encode($username.':'.$password);

    });
```

## Get a collection of items

```php
    // Retrieve all entities from the "People" Entity Set
    // Issues an HTTP GET request to http://services.odata.org/V4/TripPinService/People
    $people = $odataClient->from('People')->get();
```

## Get a specific item from a collection by ID

```php
    // Or retrieve a specific entity by the Entity ID/Key
    // Issues an HTTP GET request to http://services.odata.org/V4/TripPinService/People('russellwhyte')
    $person = $odataClient->from('People')->find('russellwhyte');
    echo "Hello, I am $person->FirstName ";
```

## Get only certain properties/columns from a collection

```php
    // Select only the "FirstName" and "LastName" properties
    // Issues an HTTP GET request to http://services.odata.org/V4/TripPinService/People?$select=FirstName,LastName
    $people = $odataClient->from('People')->select('FirstName','LastName')->get();
```

## Get a collection of items with an enum property

```php
    // Retrieve all entities where gender is female
    $person = $odataClient->from('People')->where('Gender', '=', 'Microsoft.OData.Service.Sample.TrippinInMemory.Models.PersonGender\'Female\'')->get();
```

## Top a collection

```php
    // Return the first two entities from the "People" Entity Set
    $people = $odataClient->from('People')->take(2)->get();
```

## Skip a collection

```php
    // Skip the first two entities from the "People" Entity Set
    $people = $odataClient->from('People')->skip(2)->get();
```

## Filter a collection

```php
    // Select only records whose FirstName = "Russell"
    // Issues an HTTP GET request to http://services.odata.org/V4/TripPinService/People?$filter=FirstName eq 'Russell'
    $people = $odataClient->from('People')->where('FirstName','=','Russell')->get();

    // Omitting the "=" operator defaults to "=" operator (synonymous with the request above)
    $people = $odataClient->from('People')->where('FirstName','Russell')->get();

    // Select records whose FirstName is Russel or whose LastName is Ketchum
    $people = $builder->from($entitySet)->where('FirstName','Russell')->orWhere('LastName','Ketchum')->get();
```

## Sort a collection

```php
    // Order all entities from the "People" Entity Set by Name (default asc)
    $people = $odataClient->from('People')->order('Name')->get();

    // Order all entities from the "People" Entity Set by descending Name
    $people = $odataClient->from('People')->order('Name', 'desc')->get();

    // Order all entities from the "People" Entity Set by asc ID and desc Name
    $people = $odataClient->from('People')->order(['Id', 'asc'], ['Name', 'desc'])->get();
    $people = $odataClient->from('People')->order(['column' => 'Id', 'direction' => 'asc'], ['column' => 'Name', 'direction' => 'desc'])->get();

    // Order all entities from the "People" Entity Set by asc ID and desc Name
    $order = array(['Id', 'asc'], ['Name', 'desc']);
    $order = array(['column' => 'Id', 'direction' => 'asc'], ['column' => 'Name', 'direction' => 'desc']);
    $people = $odataClient->from('People')->order($order)->get();
```

## Expand a collection

```php
    // Expand all entities from the "People" Entity by PersonGender
    $people = $odataClient->from('People')->expand('PersonGender')->get();

    // Expand all entities from the "People" Entity by PersonGender and PersonOccupation
    $people = $odataClient->from('People')->expand(['PersonGender', 'PersonOccupation'])->get();
```

## Count a collection

```php
    // Count all entities from the "People" Entity Set
    $countPeople = $odataClient->from('People')->count();
```

## Chain Params

```php
    // Retrieve a subset of entities from the "People" Entity Set
    $people = $odataClient->from($entitySet)
                          ->select('Name,Gender')
                          ->where('Gender', '=', 'Female')
                          ->order('Name', 'desc')
                          ->take(5);
                          ->get();
```
