# RESTful Services with ASP.NET Web APIs

## Overview
This section focuses on building RESTful services using ASP.NET Web APIs. You'll learn how to create and consume APIs that follow REST principles, enabling seamless communication between clients and servers.

### What Are RESTful Services?
REST (Representational State Transfer) is an architectural style for designing networked applications. It utilizes standard HTTP methods (GET, POST, PUT, DELETE) to interact with resources, making it stateless and scalable.

### Key Concepts
- **Resources**: Represented by URLs (e.g., `/api/customers`).
- **HTTP Methods**: 
  - **GET**: Retrieve data.
  - **POST**: Create new resources.
  - **PUT**: Update existing resources.
  - **DELETE**: Remove resources.
- **Stateless Communication**: Each request from a client contains all the information needed to process the request.

## Creating a Customer API Controller

Create a simple Customer API Controller that shows how to implement RESTful services in ASP.NET Web APIs based on our Customer Model.

### Setup for WEB API

```
Visual Studio has added the full set of dependencies for ASP.NET Web API 2 to project 'Vidly'. 

The Global.asax.cs file in the project may require additional changes to enable ASP.NET Web API.

1. Add the following namespace references:

    using System.Web.Http;
    using System.Web.Routing;

2. If the code does not already define an Application_Start method, add the following method:

    protected void Application_Start()
    {
    }

3. Add the following lines to the beginning of the Application_Start method:

    GlobalConfiguration.Configure(WebApiConfig.Register);
```
### Step 1: Create the Customer Controller

Create a controller to handle HTTP requests related to customers.

```csharp
using System.Collections.Generic;
using System.Data.Entity;
using System.Linq;
using System.Net;
using System.Net.Http;
using System.Web.Http;

public class CustomersController : ApiController
{
    private readonly ApplicationDbContext _context;

    public CustomersController()
    {
        _context = new ApplicationDbContext();
    }

    // GET: api/customers
    [HttpGet]
    public IEnumerable<Customer> GetCustomers()
    {
        return _context.Customers.ToList();
    }

    // GET: api/customers/5
    [HttpGet]
    public IHttpActionResult GetCustomer(int id)
    {
        var customer = _context.Customers.Find(id);
        if (customer == null)
        {
            return NotFound();
        }
        return Ok(customer);
    }

    // POST: api/customers
    [HttpPost]
    public IHttpActionResult PostCustomer(Customer customer)
    {
        if (!ModelState.IsValid)
        {
            return BadRequest(ModelState);
        }
        _context.Customers.Add(customer);
        _context.SaveChanges();
        return CreatedAtRoute("DefaultApi", new { id = customer.Id }, customer);
    }

    // PUT: api/customers/5
    [HttpPut]
    public IHttpActionResult PutCustomer(int id, Customer customer)
    {
        if (!ModelState.IsValid || id != customer.Id)
        {
            return BadRequest(ModelState);
        }
        _context.Entry(customer).State = EntityState.Modified;

        try
        {
            _context.SaveChanges();
        }
        catch (DbUpdateConcurrencyException)
        {
            if (!_context.Customers.Any(c => c.Id == id))
            {
                return NotFound();
            }
            throw;
        }

        return StatusCode(HttpStatusCode.NoContent);
    }

    // DELETE: api/customers/5
    [HttpDelete]
    public IHttpActionResult DeleteCustomer(int id)
    {
        var customer = _context.Customers.Find(id);
        if (customer == null)
        {
            return NotFound();
        }
        _context.Customers.Remove(customer);
        _context.SaveChanges();
        return Ok(customer);
    }

    protected override void Dispose(bool disposing)
    {
        if (disposing)
        {
            _context.Dispose();
        }
        base.Dispose(disposing);
    }
}
```
Step 2: Testing the API
You can use tools like Postman or cURL to test your API endpoints:

- **GET** `/api/customers` - Retrieve all customers.
- **GET** `/api/customers/{id}` - Retrieve a specific customer by ID.
- **POST** `/api/customers` - Create a new customer (send JSON in the body).
- **PUT** `/api/customers/{id}` - Update an existing customer (send JSON in the body).
- **DELETE** `/api/customers/{id}` - Delete a customer by ID.


### Create the Movies Controller
Following similar steps inside the `Controllers/Api` folder, create a `MoviesController`.

## Summary
In this section, we've covered the fundamentals of building RESTful services using ASP.NET Web APIs. You now have a working Customer API Controller that illustrates how to manage customers through standard HTTP methods. 
