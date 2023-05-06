# nosql-challenge
Module 12 submission for UC Berkeley data science bootcamp.

## Background
The UK Food Standards Agency evaluates various establishments across the United Kingdom, and gives them a food hygiene rating. This NoSQL project assumes that we have been contracted by the editors of a food magazine, _Eat Safe, Love_, to evaluate some of the ratings data in order to help their journalists and food critics decide where to focus future articles.

### Part 1 — Database Set Up
1. Import the data contained in `establishments.json` file from the terminal. Instantiate a database named `uk_food` and a collection named `establishments`. From within the `Resources` directory, the dataset was imported with the following command: `mongoimport --type json -d uk_food -c establishments --drop --jsonArray establishments.json`.

2. Import required libraries: `PyMongo` and `pprint` (PrettyPrint).

3. Create an instance of the Mongo Client.

4. Confirm that you created the database and loaded the data properly:
    - List the databases we have in MongoDB. Confirm that `uk_food` is listed.
    - List the collection(s) in the database to ensure that `establishments` is there.
    - Find and display one document in the establishments collection using `find_one` and display with `pprint`.
    - Assign the establishments collection to a variable to prepare the collection for use.

### Part 2 — Update the Database
The magazine editors have some requested modifications for the database before performing any queries or conducting an analysis. Make the following changes to the establishments collection:

1. An exciting new halal restaurant just opened in Greenwich, but hasn't been rated yet. The magazine has asked you to include it in your analysis. Add the following information to the database:
```
    {
        "BusinessName":"Penang Flavours",
        "BusinessType":"Restaurant/Cafe/Canteen",
        "BusinessTypeID":"",
        "AddressLine1":"Penang Flavours",
        "AddressLine2":"146A Plumstead Rd",
        "AddressLine3":"London",
        "AddressLine4":"",
        "PostCode":"SE18 7DY",
        "Phone":"",
        "LocalAuthorityCode":"511",
        "LocalAuthorityName":"Greenwich",
        "LocalAuthorityWebSite":"http://www.royalgreenwich.gov.uk",
        "LocalAuthorityEmailAddress":"health@royalgreenwich.gov.uk",
        "scores":{
            "Hygiene":"",
            "Structural":"",
            "ConfidenceInManagement":""
        },
        "SchemeType":"FHRS",
        "geocode":{
            "longitude":"0.08384000",
            "latitude":"51.49014200"
        },
        "RightToReply":"",
        "Distance":4623.9723280747176,
        "NewRatingPending":True
    }
```

2. Find the BusinessTypeID for "Restaurant/Cafe/Canteen" and return only the BusinessTypeID and BusinessType fields.

3. Update the new restaurant with the BusinessTypeID you found.

4. The magazine is not interested in any establishments in Dover, so check how many documents contain the Dover Local Authority. Then, remove any establishments within the Dover Local Authority from the database, and check the number of documents to ensure they were deleted.

5. Some of the number values are stored as strings, when they should be stored as numbers.

    - Use update_many to convert latitude and longitude to decimal numbers.
    - Use update_many to convert RatingValue to integer numbers.
    
### Part 3: Exploratory Analysis
_Eat Safe, Love_ has specific questions they want answered, which will help them find the locations they wish to visit and avoid.

Some notes to be aware of during our exploratory analysis:

- `RatingValue` refers to the overall rating decided by the Food Authority and ranges from 1-5. The higher the value, the better the rating.
    - **Note:** This field also includes non-numeric values such as 'Pass', where 'Pass' means that the establishment passed their inspection but isn't given a number rating. We will coerce non-numeric values to nulls during the database setup before converting ratings to integers.
- The scores for Hygiene, Structural, and ConfidenceInManagement work in reverse. This means, the higher the value, the worse the establishment is in these areas.

Use the following questions to explore the database, and find the answers to provide the magazine editors. Unless otherwise stated, for each question:
    - Use `count_documents` to display the number of documents contained in the result.
    - Display the first document in the results using `pprint`.
    - Convert the result to a Pandas DataFrame, print the number of rows in the DataFrame, and display the first 10 rows.

1. Which establishments have a hygiene score equal to 20?

2. Which establishments in London have a `RatingValue` greater than or equal to 4? The London Local Authority has a longer name than "London" so we will need to use `$regex` as part of our search.

3. What are the top 5 establishments with a `RatingValue` of 5, sorted by lowest hygiene score, nearest to the new restaurant added, "Penang Flavours"? We will need to compare the geocode to find the nearest locations. Search within 0.01 degree on either side of the latitude and longitude.

4. How many establishments in each Local Authority area have a hygiene score of 0? Sort the results from highest to lowest, and print out the top ten local authority areas. We will need to use the aggregation method to answer this. The first 5 rows of our resulting DataFrame should look something like this:

    | \_id | count |
    |------|-------|
    | 0 | Thanet | 1130 |
    | 1 | Greenwich | 882 |
    | 2 | Maidstone | 713 |
    | 3 | Newham | 711 |
    | 4 | Swale | 686 |