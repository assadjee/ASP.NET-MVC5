# RESTful Services with ASP.NET Web APIs and Client Side Development

## Data Transfer Objects
**What is a DTO?**
A Data Transfer Object (DTO) is an object that carries data between processes. It is often used to encapsulate data and send it from one subsystem of an application to another. In the context of the Vidly project, DTOs help to manage the data being sent to and from the client and server, improving the separation of concerns and enhancing security.

## Purpose of Using DTOs

- **Encapsulation**: DTOs can encapsulate data from multiple sources and limit the amount of data sent over the network.
- **Security**: By using DTOs, you can expose only the necessary fields to the client, protecting sensitive data.
- **Decoupling**: Changes in the database model do not necessarily require changes in the API, as the DTO can serve as a stable interface.

## Implementation Code for DTOs

### Step 1: Create DTOs

- **Customer DTO**

Create a DTO class that represents the data you want to expose for customers. For example, create a `CustomerDto` class.

```csharp
public class CustomerDto
{
    public int Id { get; set; }
    public string Name { get; set; }
    public bool IsSubscribedToNewsLetter { get; set; }
    public byte MembershipTypeId { get; set; }
    public MembershipTypeDto MembershipType { get; set; }
}
```

- **MembershipType DTO**
Create a DTO class for MembershipType, `MembershipTypeDTO`

```csharp
 public class MembershipTypeDto
 {
     public byte Id { get; set; }
     public string Name { get; set; }
 }
```
- **Movie DTO**
```csharp
public class MovieDto
{
    public int Id { get; set; }
    [Required]
    [StringLength(255)]
    public string Name { get; set; }
    [Required]
    public byte GenreId { get; set; }
    public GenreDto Genre { get; set; }
    [Required]
    public DateTime ReleaseDate { get; set; }
    public DateTime? DateAdded { get; set; }
    [Required]
    [Range(1, 20)]
    public int NumberInStock { get; set; }
}
```
- **Genre DTO**
```csharp
public class GenreDto
{
    public byte Id { get; set; }
    public string Name { get; set; }
}
```

### Use Auto Mapper

#### Overview of AutoMapper

**What is AutoMapper?**
AutoMapper is a popular object-to-object mapping library that helps simplify the process of mapping properties from one object to another. It is particularly useful when working with DTOs (Data Transfer Objects) and entity models in applications like the Vidly project.

#### Purpose of Using AutoMapper

- **Reduce Boilerplate Code**: AutoMapper eliminates the need for repetitive code when mapping properties between objects.
- **Maintainability**: By centralizing mapping configurations, it simplifies updates and modifications.
- **Type Safety**: AutoMapper checks for type compatibility at compile time, reducing runtime errors.

#### Implementation Code for AutoMapper

- **Install AutoMapper**

You can install AutoMapper via NuGet Package Manager. In the Package Manager Console, run:

```bash
Install-Package AutoMapper version:4.1
```

- **Create Mapping Profiles**
Create a mapping profile class to define how to map between your Customer model and CustomerDto. Create a new class named `MappingProfile` in `App_Start`.

```csharp
using AutoMapper;
```
```csharp
public class MappingProfile : Profile
{
    public MappingProfile()
    {
            //Domain to Dto
            Mapper.CreateMap<Customer, CustomerDto>();
            Mapper.CreateMap<Movie, MovieDto>();
            Mapper.CreateMap<MembershipType, MembershipTypeDto>();
            Mapper.CreateMap<Genre, GenreDto>();

            //Dto to Domain
            Mapper.CreateMap<CustomerDto, Customer>().ForMember(c => c.Id, opt => opt.Ignore());
            Mapper.CreateMap<MovieDto, Movie>().ForMember(m => m.Id, opt => opt.Ignore());
    }
}
```

- **Configure AutoMapper**
In the Global.asax.cs file, configure AutoMapper during application startup.


```csharp
using AutoMapper;
public class MvcApplication : System.Web.HttpApplication
{
    protected void Application_Start()
    {
        // Other configurations...

        Mapper.Initialize(cfg => cfg.AddProfile<MappingProfile>());
    }
}
```
### Step 2: Modify the Customers Controller
Update the CustomersController to use the CustomerDto instead of the Customer model directly. This involves converting the Customer entities to CustomerDto objects when returning data.

```csharp
public IEnumerable<CustomerDto> GetCustomers()
        {
            var customers = _context.Customers
                .Include(c => c.MembershipType);            
            return customers
                .ToList()
                .Select(Mapper.Map<Customer, CustomerDto>);
        }
```

- Get Method (`GET /api/customers/1`)
```csharp
public IHttpActionResult GetCustomer(int id)
        {
            var customer = _context.Customers.SingleOrDefault(c => c.Id == id);

            if (customer == null)
                return NotFound();

            return Ok(Mapper.Map<Customer, CustomerDto>(customer));
        }
```
- Post Method

```csharp
//POST /api/customers
        [HttpPost]
        public IHttpActionResult CreateCustomer(CustomerDto customerDto)
        {
            if (!ModelState.IsValid)
                return BadRequest();
            //throw new HttpResponseException(HttpStatusCode.BadRequest);

            var customer = Mapper.Map<CustomerDto, Customer>(customerDto);
            _context.Customers.Add(customer);
            _context.SaveChanges();
            customerDto.Id = customer.Id;
            return Created(Request.RequestUri + "/" + customer.Id, customerDto);
        }
```

- Put Method

```csharp
//PUT api/customers/1
        [HttpPut]
        public IHttpActionResult UpdateCustomer(int id, CustomerDto customerDto)
        {
            if (!ModelState.IsValid)
                return BadRequest();
            //throw new HttpResponseException(HttpStatusCode.BadRequest);


            var customerInDb = _context.Customers.SingleOrDefault(c => c.Id == id);
            if (customerInDb == null)
                return NotFound();

            Mapper.Map(customerDto, customerInDb);

            _context.SaveChanges();
            return Ok(customerDto);
        }
```

- Put Method

```csharp
//DELETE /api/customers/1
        [HttpDelete]
        public IHttpActionResult DeleteCustomer(int id)
        {
            var customerInDb = _context.Customers.SingleOrDefault(c => c.Id == id);
            if (customerInDb == null)
                return NotFound();

            _context.Customers.Remove(customerInDb);
            _context.SaveChanges();
            return Ok();
        }
```


#### Movies Controller (update as above)

### Camel Notation for Results from API

How to fix this issue:
We need to configure the WebAPI to return `json` object using Camel notation.

- In `WebApiConfig` in `App_Start`

add the following:

```csharp
//include the following as well:
using Newtonsoft.Json.Serialization;
```
```csharp
var settings =  config.Formatters.JsonFormatter.SerializerSettings;
            settings.ContractResolver = new CamelCasePropertyNamesContractResolver();
            settings.Formatting = Formatting.Indented;
```
