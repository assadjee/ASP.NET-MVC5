# End to End Feature ImplementationRenting Out Movies in the Vidly Project

## Overview

In this section, we will implement a rental feature in the Vidly project, allowing customers to rent movies. We will cover the entire process, including planning, designing, and coding the feature systematically.

## Objectives

- Enable customers to rent movies.
- Integrate the rental feature with existing Customer and Movie models.
- Ensure proper validation and error handling.

## Topics Covered

1. **Feature Planning**
2. **Designing the Rental Feature**
3. **Setting Up the Development Environment**
4. **Implementing the Rental Feature**
   - Models
   - Views
   - Controllers
5. **Testing the Rental Feature**
6. **Deployment Considerations**
7. **Documentation**

## Detailed Steps

### Step 1: Feature Planning

- **Define Requirements**: Customers should be able to select a movie and rent it for a specified duration.
- **User Stories**:
  - As a customer, I want to see available movies to rent.
  - As a customer, I want to rent a movie and specify a return date.
- **Acceptance Criteria**: 
  - The rental feature should display available movies.
  - The rental process should save the rental details to the database.

### Step 2: Designing the Rental Feature

1. **Wireframes**: Create sketches for the rental interface, including a list of movies and a rental form.
2. **Database Schema**: Ensure the `Rental` model links to `Customer` and `Movie`.

### Step 3: Setting Up the Development Environment

- Ensure your development environment is configured correctly.
- Create a new branch in your version control system for the rental feature.

### Step 4: Implementation

#### A. Models

Create the `Rental` model to handle movie rentals.

**Rental.cs**
```csharp
public class Rental
{
    public int Id { get; set; }
    public int CustomerId { get; set; }
    public int MovieId { get; set; }
    public DateTime RentalDate { get; set; }
    public DateTime ReturnDate { get; set; }

    public virtual Customer Customer { get; set; }
    public virtual Movie Movie { get; set; }
}
```
#### Dto

```csharp
  public class MovieRentalDto
    {
       
        public int CustomerId { get; set; }
        public List<int> MovieIds { get; set; }
    }
```
#### B. Controller  (API Controller)
```csharp
public class MovieRentalsController : ApiController
    {
        ApplicationDbContext _context = new ApplicationDbContext();

        [HttpPost]
        public IHttpActionResult CreateNewRentals(MovieRentalDto movieRentalDto)
        {
            var customer = _context.Customers.Single(
                c => c.Id == movieRentalDto.CustomerId);

            var movies = _context.Movies.Where(
                m => movieRentalDto.MovieIds.Contains(m.Id)).ToList();

            foreach (var movie in movies)
            {
                if (movieRentalDto.MovieIds.Count == 0)
                return BadRequest("No Movie Ids have been given.");
                movie.NumberAvailable--;
                var rental = new Rental
                {
                    Customer = customer,
                    Movie = movie,
                    DateRented = DateTime.Now
                };
                
                _context.Rentals.Add(rental);
            }

            _context.SaveChanges();
            return Ok();
        }

    }

```
###  MovieController

```csharp
public class MoviesController : ApiController
{
    private ApplicationDbContext _context;
    public MoviesController()
    {               
        _context = new ApplicationDbContext();
    }

    // GET: api/Movies
    public IEnumerable<MovieDto> GetMovies()
    {
        var movies = _context.Movies
                        .Include(c => c.Genre);
        return movies
            .ToList()
            .Select(Mapper.Map<Movie, MovieDto>);
    }

    // GET: api/Movies/5
    [ResponseType(typeof(Movie))]
    public IHttpActionResult GetMovie(int id)
    {
        var movie = _context.Movies.SingleOrDefault(c => c.Id == id);

        if (movie == null)
            return NotFound();

        return Ok(Mapper.Map<Movie, MovieDto>(movie));
    }

    // PUT: api/Movies/5
    [ResponseType(typeof(void))]
    public IHttpActionResult PutMovie(int id, MovieDto movieDto)
    {
        if (!ModelState.IsValid)
            return BadRequest();
        //throw new HttpResponseException(HttpStatusCode.BadRequest);

        var movieInDb = _context.Movies.SingleOrDefault(c => c.Id == id);
        if (movieInDb == null)
            return NotFound();
        bool stockIncreased = false;
        int numberAvailable = movieInDb.NumberAvailable;
        //Implement logic for Available movies
        if(movieDto.NumberInStock > movieInDb.NumberInStock)
        {
            stockIncreased = true;
            if(movieInDb.NumberInStock == movieInDb.NumberAvailable )
                numberAvailable += movieDto.NumberInStock - movieInDb.NumberInStock; 
        }
        Mapper.Map(movieDto, movieInDb);
        movieInDb.NumberAvailable = numberAvailable;
        _context.SaveChanges();
        return Ok(movieDto);
    }

    // POST: api/Movies
    [ResponseType(typeof(Movie))]
    public IHttpActionResult PostMovie(MovieDto movieDto)
    {
        if (!ModelState.IsValid)
            return BadRequest();
        //throw new HttpResponseException(HttpStatusCode.BadRequest);

        var movie = Mapper.Map<MovieDto, Movie>(movieDto);
        movie.NumberAvailable = movie.NumberInStock;
        _context.Movies.Add(movie);
        _context.SaveChanges();
        movieDto.Id = movie.Id;
        return Created(Request.RequestUri + "/" + movie.Id, movieDto);
    }

    // DELETE: api/Movies/5
    [ResponseType(typeof(Movie))]
    public IHttpActionResult DeleteMovie(int id)
    {
        var movieInDb = _context.Movies.SingleOrDefault(c => c.Id == id);
        if (movieInDb == null)
            return NotFound();

        _context.Movies.Remove(movieInDb);
        _context.SaveChanges();
        return Ok();
    }

    protected override void Dispose(bool disposing)
    {
        if (disposing)
        {
            _context.Dispose();
        }
        base.Dispose(disposing);
    }

    private bool MovieExists(int id)
    {
        return _context.Movies.Count(e => e.Id == id) > 0;
    }
}
```
### Misc        
- Update `Movie` model 
- Add `public int NumberAvailable { get; set; } ` 
- Add Migration and add it to the database
- Make necessary changes in the Controllers (including API Controller)

#### C. View

#### Install auto-completion package:
- `install-package Twitter.Typeahead`

- Add the package in bundle config: `"~/Scripts/typeahead.bundle.js"`

- Install the css for the package
- Search for typeahead.js
- Go to examples page, get the css file from source
- Copy styles from `example.css`: `.typeahead` and put them to the stylesheet document `typeahead.css`

```css
.typeahead {
    background-color: #fff;
}

.typeahead:focus {
    border: 2px solid #0097cf;
}

.tt-query {
    -webkit-box-shadow: inset 0 1px 1px rgba(0, 0, 0, 0.075);
    -moz-box-shadow: inset 0 1px 1px rgba(0, 0, 0, 0.075);
    box-shadow: inset 0 1px 1px rgba(0, 0, 0, 0.075);
}

.tt-hint {
    color: #999
}

.tt-menu {
    width: 422px;
    margin: 12px 0;
    padding: 8px 0;
    background-color: #fff;
    border: 1px solid #ccc;
    border: 1px solid rgba(0, 0, 0, 0.2);
    -webkit-border-radius: 8px;
    -moz-border-radius: 8px;
    border-radius: 8px;
    -webkit-box-shadow: 0 5px 10px rgba(0,0,0,.2);
    -moz-box-shadow: 0 5px 10px rgba(0,0,0,.2);
    box-shadow: 0 5px 10px rgba(0,0,0,.2);
}

.tt-suggestion {
    padding: 3px 20px;
    font-size: 18px;
    line-height: 24px;
}

.tt-suggestion:hover {
    cursor: pointer;
    color: #fff;
    background-color: #0097cf;
}

.tt-suggestion.tt-cursor {
    color: #fff;
    background-color: #0097cf;

}

.tt-suggestion p {
    margin: 0;
}

tt-container {
    position: relative;
}
```
- Add css to the bundle `"~/Content/typeahead.css"`
- Add an emptyy View into Rental Views.
```html
@model dynamic
@{
    ViewBag.Title = "New Rental Form";
}
@*Calls actions mvc controller not api controller*@
@*@Ajax.BeginForm()*@
<form id="newRental">
    <div class="form-group">
        <label>Customer</label>
        <div class="tt-container">
            <input id="customer" name="customer" data-rule-validCustomer="true" required type="text" value="" class="form-control" />
        </div>
    </div>

    <div class="form-group">
        <label>Movie</label>
        <div class="tt-container">
            <input id="movie" name="movie" data-rule-atLeastOneMovie="true" type="text" value="" class="form-control" />
        </div>
    </div>

    <div class="row">
        <div class="col-md-4 col-sm-4">
            <ul id="movies" class="list-group"></ul>
        </div>
    </div>

    <button class="btn btn-primary">Submit</button>
</form>

@section scripts
{
    @Scripts.Render("~/bundles/jqueryval")
    <script>
        $(function() {
            var vm = {
                movieIds: []
            };
            var customers = new Bloodhound({
                datumTokenizer: Bloodhound.tokenizers.obj.whitespace('name'),
                queryTokenizer: Bloodhound.tokenizers.whitespace,

                remote: {
                    url: '/api/customers?query=%QUERY',
                    wildcard: '%QUERY'
                }
            });

            $('#customer').typeahead({
                    minLength: 3,
                    highlight: true
                },
                {
                    name: 'customers',
                    display: 'name',
                    source: customers
                }).on("typeahead:select",
                function(e, customer) {
                    vm.customerId = customer.id;
                });


            var movies = new Bloodhound({
                datumTokenizer: Bloodhound.tokenizers.obj.whitespace('name'),
                queryTokenizer: Bloodhound.tokenizers.whitespace,

                remote: {
                    url: '/api/movies?query=%QUERY',
                    wildcard: '%QUERY'
                }
            });


            $('#movie').typeahead({
                    minLength: 3,
                    highlight: true
                },
                {
                    name: 'movies',
                    display: 'name',
                    source: movies
                }).on("typeahead:select",
                function(e, movie) {
                    $("#movies").append("<li class='list-group-item'>" + movie.name + "</li>");
                    $("#m ovie").typeahead("val", "");
                    vm.movieIds.push(movie.id);
                });

            $.validator.addMethod("validCustomer",
                function() {
                    return vm.customerId && vm.customerId != 0;
                },
                "Please select a valid customer");

            $.validator.addMethod("atleastOneMovie",
                function() {
                    return vm.movieIds.length > 0;
                },
                "Please select at least One Movie");

            var validator = $("#newRental").validate({
                submitHandler: function() {
                    $.ajax({
                            url: "/api/MovieRentals",
                            method: "post",
                            data: vm
                        })
                        .done(function() {
                            toastr.success("Rentals successfully recorded..");

                            $("#customer").typeahead("val", "");
                            $("#movie").typeahead("val", "");
                            $("#movies").empty();

                            vm = {
                                movieIds: []
                            };
                            validator.resetForm();
                        })
                        .fail(function() {
                            toastr.error("Something unexpected happend..");
                        });
                    return false;
                }
            });
        });
    </script>

}

```

### Fixes
- In `CustomerController` in APIControllers, for `GetCustomers` action, add `(string query = null)` as argument and following inside action:

```csharp
var customers = _context.Customers
    .Include(c => c.MembershipType);
if (!String.IsNullOrWhiteSpace(query))
    customers = customers.Where(c => c.Name.Contains(query));

return customers
    .ToList()
    .Select(Mapper.Map<Customer, CustomerDto>); 
```

- In Movies Controller, filter on the movie names and those which are available

```csharp
//GET api/movies
        public IEnumerable<MovieDto> GetMovies(string query = null)
        {
            var movies = _context
                .Movies
                .Include(m => m.Genre)
                .Where(m => m.NumberAvailable > 0);

            if (!String.IsNullOrWhiteSpace(query))
                movies = movies.Where(m => m.Name.Contains(query));

            return movies
                .ToList()
                .Select(Mapper.Map<Movie, MovieDto>);
        }
```

### Display Toast Notifications

- Install the package `toastr` using package manager: `install-package toastr`
- Add JS bundle and css bundle in BundleConfig accordingly.
- `"~/Scripts/toastr.js"` and
- `"~/Content/toastr.css"`

## Deployment
- Application Publish Method: Publish on File System

- Database: `update-database -script -SourceMigration:MigrationName`
- Import to the custom database or handover to DBA to deploy the database


