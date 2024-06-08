---
{"dg-publish":true,"permalink":"/concepts/querying-multiple-tables/","dgHomeLink":true,"dgShowToc":true}
---

# Querying Multiple Tables

When completing [[Tutorials/Creating a To-do List App, Part 6\|part six of the to-do list tutorial series]] you learned how to use the MVVM design pattern to query a single table hosted at Supabase.

When a database contains multiple tables, there will necessarily be [[Concepts/Entity-Relationship Diagrams\|relationships]] between those tables.

How, though, do we read data from, or write data to, tables that are related to other tables?

Read on to find out.

## Countries and cities

How is a country related to a city? How is a city related to a country?

### ER diagram

Consider this entity-relationship diagram identifying how a country is related to a city:

![IMG_4312 Large.png](/img/user/Media/IMG_4312%20Large.png)

Thinking of the relationship in each direction:

- country to city
	- A given country has *many* cities.
- city to country
	- A given city belongs to *one* country.

Looking from country to city, overall now, there is a *one-to-many* relationship.

Looking from city to country, overall now, there is a *many-to-one* relationship.

### Creating a database

Using a [[Concepts/Entity-Relationship Diagrams#Conversion to a database\|large language model such as ChatGPT]], we can obtain SQL that will create a database that matches the ER diagram above, and populate it with sample data.

Here is the prompt Mr. Gordon used to do so:

```
This is a photo of a hand-drawn conceptual entity-relationship diagram. The diagram describes the relationship between countries and cities.

Please convert this image into valid SQL that will create database tables with columns, primary keys, and foreign keys where appropriate based on the cardinality described in the diagram.

Do your best to infer data types for table columns based on the name of attributes given for each entity.

Be certain that the SQL you generate is compatible with the PostgreSQL database software.

Once you have produced SQL that creates a database that matches this ER diagram, please create some reasonable sample data for each table. Ensure that the following countries are represented: Canada, United States, China, Germany, and Mexico. For each country provide two major cities.
```

Here is the response Mr. Gordon received from ChatGPT:

> Here is the SQL code to create the database tables as described in the provided ER diagram, along with the sample data for each table:

```sql
-- Create the country table
CREATE TABLE Country (
    id SERIAL PRIMARY KEY,
    name VARCHAR(100) NOT NULL
);

-- Create the city table
CREATE TABLE City (
    id SERIAL PRIMARY KEY,
    name VARCHAR(100) NOT NULL,
    country_id INT NOT NULL,
    FOREIGN KEY (country_id) REFERENCES Country(id)
);

-- Insert sample data into the country table
INSERT INTO Country (name) VALUES ('Canada');
INSERT INTO Country (name) VALUES ('United States');
INSERT INTO Country (name) VALUES ('China');
INSERT INTO Country (name) VALUES ('Germany');
INSERT INTO Country (name) VALUES ('Mexico');

-- Insert sample data into the city table
INSERT INTO City (name, country_id) VALUES ('Toronto', 1);
INSERT INTO City (name, country_id) VALUES ('Vancouver', 1);
INSERT INTO City (name, country_id) VALUES ('New York', 2);
INSERT INTO City (name, country_id) VALUES ('Los Angeles', 2);
INSERT INTO City (name, country_id) VALUES ('Beijing', 3);
INSERT INTO City (name, country_id) VALUES ('Shanghai', 3);
INSERT INTO City (name, country_id) VALUES ('Berlin', 4);
INSERT INTO City (name, country_id) VALUES ('Munich', 4);
INSERT INTO City (name, country_id) VALUES ('Mexico City', 5);
INSERT INTO City (name, country_id) VALUES ('Guadalajara', 5);
```

> **Explanation:**
> 1. **Country Table**:
>    - `id`: Unique identifier for each country (Primary Key).
>    - `name`: Name of the country.
> 
> 2. **City Table**:
>    - `id`: Unique identifier for each city (Primary Key).
>    - `name`: Name of the city.
>    - `country_id`: Foreign key referencing the `id` column in the `Country` table, establishing the relationship between cities and their respective countries.
> 
> **Sample Data:**
> - **Countries**:
>   - Canada
>   - United States
>   - China
>   - Germany
>   - Mexico
> 
> - **Cities**:
>   - Canada: Toronto, Vancouver
>   - United States: New York, Los Angeles
>   - China: Beijing, Shanghai
>   - Germany: Berlin, Munich
>   - Mexico: Mexico City, Guadalajara
> 
> This SQL script sets up the database schema and populates it with sample data for the specified countries and cities.

The response was an accurate representation of the ER diagram. Mr. Gordon prefers to use lowercase names for database tables, however, so he asked the LLM to revise the SQL so that the `Country` table was named `country`, for example, using this prompt:

```
Please revise the SQL you created so that database table names are always lowercase.
```

### Reviewing the schema

After that adjustment, Mr. Gordon created a new project at [Supabase](https://supabase.com), then pasted the SQL into the SQL Editor and ran the query:

![Screenshot 2024-06-08 at 12.41.21 PM.png](/img/user/Media/Screenshot%202024-06-08%20at%2012.41.21%E2%80%AFPM.png)

By then navigating to the **Database** panel:

![Screenshot 2024-06-08 at 12.41.35 PM.png](/img/user/Media/Screenshot%202024-06-08%20at%2012.41.35%E2%80%AFPM.png)

Then selecting **Schema Visualizer**:

![Screenshot 2024-06-08 at 12.43.14 PM.png](/img/user/Media/Screenshot%202024-06-08%20at%2012.43.14%E2%80%AFPM.png)

Mr. Gordon was able to obtain a visual representation of the table structure – this is called the *schema* for a database.

Let's compare the ER diagram to the database schema:

![Screenshot 2024-06-08 at 12.48.50 PM.png](/img/user/Media/Screenshot%202024-06-08%20at%2012.48.50%E2%80%AFPM.png)

Two tables were created; attributes that are underlined are converted into *primary keys*. A primary key is a unique identifier for a row in a database. Values are typically integers that are auto-incremented for each new row that is added to the database table.

The `city` table has a *foreign key* connecting it to the `country` table.

Reviewing the data used to populate these tables, we can see how the foreign key ties multiple cities to a single country:

![Screenshot 2024-06-08 at 12.56.28 PM.png](/img/user/Media/Screenshot%202024-06-08%20at%2012.56.28%E2%80%AFPM.png)

### Creating a project

To query the data in this newly created database, Mr. Gordon:

- created a new project in Xcode, ensuring source control was enabled
- created a remote
- created groups for the model, views, view models, and helper code
- created a file named `Supabase.swift` that contains the database information required to connect to his database hosted at Supabase

Like this:

![Screenshot 2024-06-08 at 1.08.19 PM.png](/img/user/Media/Screenshot%202024-06-08%20at%201.08.19%E2%80%AFPM.png)

The database connection information was obtained from the **API** section of **Project Settings**:

![Screenshot 2024-06-08 at 1.06.00 PM.png](/img/user/Media/Screenshot%202024-06-08%20at%201.06.00%E2%80%AFPM.png)

Next Mr. Gordon added the [[Tutorials/Creating a To-do List App, Part 6#Adding the Supabase framework\|package dependency for Supabase]]:

![Screenshot 2024-06-08 at 1.09.33 PM.png](/img/user/Media/Screenshot%202024-06-08%20at%201.09.33%E2%80%AFPM.png)

He then made sure that all six package products were being compiled into the app:

![Screenshot 2024-06-08 at 1.10.18 PM.png](/img/user/Media/Screenshot%202024-06-08%20at%201.10.18%E2%80%AFPM.png)

Before proceeding, Mr. Gordon committed his work.

#### Listing countries alone

To simply list the countries in the database, Mr. Gordon at first created a view with a couple of static values:

![Screenshot 2024-06-08 at 1.14.51 PM.png](/img/user/Media/Screenshot%202024-06-08%20at%201.14.51%E2%80%AFPM.png)

After committing that work, Mr. Gordon next created a `Country` model:

![Screenshot 2024-06-08 at 1.19.56 PM.png](/img/user/Media/Screenshot%202024-06-08%20at%201.19.56%E2%80%AFPM.png)

... to match the `country` database table:

![Screenshot 2024-06-08 at 1.20.12 PM.png](/img/user/Media/Screenshot%202024-06-08%20at%201.20.12%E2%80%AFPM.png)

After committing that work, Mr. Gordon then created a view model, to load countries data from the database:

![Screenshot 2024-06-08 at 1.33.48 PM.png](/img/user/Media/Screenshot%202024-06-08%20at%201.33.48%E2%80%AFPM.png)

After committing that work, Mr. Gordon then made the view use the view model, rather than simply showing static data:

![Screenshot 2024-06-08 at 1.36.29 PM.png](/img/user/Media/Screenshot%202024-06-08%20at%201.36.29%E2%80%AFPM.png)

Mr. Gordon then committed his work.

#### Listing countries with cities (one to many)

Listing countries alone might be useful.

It would be more useful to be able to list countries – and then navigate down to the many cities that belong to the selected country.

This is an example of loading from the database when a one to many relationship exists, and is equivalent to running this SQL statement, which you learned how to do earlier this year:

```sql
SELECT 
  country.id AS "country_id",
  country.name AS "country_name",
  city.id AS "city_id",
  city.name AS "city_name",
  city.country_id AS "city_country_foreign_key"
FROM country
  INNER JOIN city
  ON country.id = city.country_id;
```

Here is what that query returns at Supabase:

![Screenshot 2024-06-08 at 1.57.37 PM.png](/img/user/Media/Screenshot%202024-06-08%20at%201.57.37%E2%80%AFPM.png)

To do this, Mr. Gordon first created a model that matches this one to many relationship between a country and it's cities:

![Screenshot 2024-06-08 at 2.02.35 PM.png](/img/user/Media/Screenshot%202024-06-08%20at%202.02.35%E2%80%AFPM.png)

After committing that work, Mr. Gordon then adjusted `CountriesListViewModel` to use the new model:

![Screenshot 2024-06-08 at 2.08.37 PM.png](/img/user/Media/Screenshot%202024-06-08%20at%202.08.37%E2%80%AFPM.png)

> [!NOTE]
> Look carefully at the argument provided to the `select` function.
> 
> Supabase automatically joins the `country` table to the `city` table for us based on the foreign key that connects these tables.
> 
> **We must be careful to use the correct column and table names.**

After committing that work, Mr. Gordon then adjusted `CountriesListView` to use the new array that contains countries along with their cities. He configured the view to show the country name along with how many cities are in a given country:

![Screenshot 2024-06-08 at 2.25.46 PM.png](/img/user/Media/Screenshot%202024-06-08%20at%202.25.46%E2%80%AFPM.png)

However, Mr. Gordon encountered a common logical error at this point. The program runs, but there are no cities displayed.

To investigate this situation, Mr. Gordon opened the debug area in Xcode and noticed an error:

![Screenshot 2024-06-08 at 2.26.50 PM.png](/img/user/Media/Screenshot%202024-06-08%20at%202.26.50%E2%80%AFPM.png)

With this error message:

```
Swift.DecodingError.keyNotFound(CodingKeys(stringValue: "cities", intValue: nil)
```

... the Swift compiler is telling us it expected to see a list of cities for a given country provided in a key named `cities`.

The issue is that Supabase is sending us the list of cities for a given country in a key named `city` – it does that since `city` is the name of the database table that holds city information:

```json
[
   {
      "id":1,
      "name":"Canada",
      "city":[
         {
            "id":1,
            "name":"Toronto"
         },
         {
            "id":2,
            "name":"Vancouver"
         }
      ]
   },
   {
      "id":2,
      "name":"United States",
      "city":[
         {
            "id":3,
            "name":"New York"
         },
         {
            "id":4,
            "name":"Los Angeles"
         }
      ]
   },
   {
      "id":3,
      "name":"China",
      "city":[
         {
            "id":5,
            "name":"Beijing"
         },
         {
            "id":6,
            "name":"Shanghai"
         }
      ]
   },
   {
      "id":4,
      "name":"Germany",
      "city":[
         {
            "id":7,
            "name":"Berlin"
         },
         {
            "id":8,
            "name":"Munich"
         }
      ]
   },
   {
      "id":5,
      "name":"Mexico",
      "city":[
         {
            "id":9,
            "name":"Mexico City"
         },
         {
            "id":10,
            "name":"Guadalajara"
         }
      ]
   }
]
```

We have two choices to fix the problem.

1. We could rename the Supabase table `city` to be named `cities` instead.
2. We could give the Swift compiler a hint, so that it knows when it sees `city` when decoding the information sent from Supabase, that it should place that information in the `cities` array.

We will choose the second option, by making a small adjustment to our model:

![Screenshot 2024-06-08 at 2.41.46 PM.png](/img/user/Media/Screenshot%202024-06-08%20at%202.41.46%E2%80%AFPM.png)

Now, returning to the list view, we see that information is loaded correctly – with a small adjustment to the `VStack` so that information is aligned on the leading edge of the view:

![Screenshot 2024-06-08 at 2.42.16 PM.png](/img/user/Media/Screenshot%202024-06-08%20at%202.42.16%E2%80%AFPM.png)

Mr. Gordon then committed his work.

##### Adding a cities list view

What we really want to do, though, is navigate from our list of countries to a list of cities in that country.

So, Mr. Gordon added a new view named `CitiesListView` that accepts a single country:

![Screenshot 2024-06-08 at 3.06.50 PM.png](/img/user/Media/Screenshot%202024-06-08%20at%203.06.50%E2%80%AFPM.png)

He then modified the `CountriesListView` so that it navigates down to  `CitiesListView`:

![Screenshot 2024-06-08 at 3.04.16 PM.png](/img/user/Media/Screenshot%202024-06-08%20at%203.04.16%E2%80%AFPM.png)

Now the app works as follows:

![RocketSim_Recording_iPhone_15_Pro_6.1_2024-06-08_15.07.34.gif|350](/img/user/Media/RocketSim_Recording_iPhone_15_Pro_6.1_2024-06-08_15.07.34.gif)

This illustrates how to read data from two database tables – `country` and `city` – when a one to many relationship exists between them.

Mr. Gordon then committed his work.

