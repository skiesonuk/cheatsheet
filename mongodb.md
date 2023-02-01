<div style="text-align: center;"> 
<img src="./MongoDB_Logo.png"></div>


1.INTRODUCTION
============

How to create a database in MongoDB?
-------------------
<pre>
  <b>use</b> dataBaseName
</pre>

How to create a collection in MongoDB?
---------------------
<pre>
db.<b>createCollection</b>( '<collectionName>',
{
capped: <boolean>,
autoIndexId: <boolean>,
size: <number>,
max: <number>,
storageEngine: <document>,
validator: <document>,
validationLevel: <string>,
validationAction: <string>,
indexOptionDefaults: <document>,
viewOn: <string>,
pipeline: <pipeline>,
collation: <document>,
writeConcern: <document>
})
</pre>

> **Insert creates a collection if it doesn't exists:**

<pre>
use yourDatabaseName;
db.myCollectionName.<b>insert</b>(
                       {"name" : "Yahya A", "company" : "Sony"}
);
</pre>

How to list collections in MongoDB?
-----------------------------------
<pre>
 <b>show collections;</b>
</pre>

How to insert a single document in MongoDB?
-----------------------------------
<pre>
db.blogs.<b>insertOne</b>(
                  { 
                    username: "Zakariya", 
                    noOfBlogs: 100, 
                    tags: ["science","fiction"]
                  }
);
</pre>

How to insert many documents in MongoDB?
----------------------------------------------
<pre>
db.blogs.<b>insertMany</b>(
                    [
                     { username: "Thaha", noOfBlogs: 200, tags: ["science","robotics"]},
                     { username: "Thayebbah", noOfBlogs: 500, tags: ["cooking","general knowledge"]},
                     { username: "Thaherah", noOfBlogs: 50, tags: ["beauty","arts"]}
                    ]
);
</pre>

How to fetch documents in MongoDB?
------------------------------------------------
<pre>
db.collection.<b>find(query, projection)</b>
</pre>
------------------------------------------------
2.SOME DATATYPES IN MONGODB & COMMAND SYNTAX
============
<pre>
var explicitInt = <b>NumberInt</b>("1299")
var explicitLong_double = <b>NumberLong</b>(444333222111242)

var uniqueID = new <b>ObjectId()</b>;
uniqueId.<b>getTimestamp()</b>;  // print document insertion time

var date = <b>Date()</b>; // will be in local timezeone
var date = <b>new  Date()</b>; //If you add the new keyword to the constructor, you get the BSON date that is wrapped in ISODate()
var date = <b>ISODate()</b>;

// The first argument to BinData is a binary subtype to indicate the type of information stored.
// Zero value stands for plain binary data and can be used with text or media files.
var binData = <b>BinData(0,"VghASDI10ZGUUu")</b>
</pre>

Database Commands Syntax in MongoDB
------------------------------------------------
<pre>
db.<b>runCommand</b>( { <db_command> } )
db.<b>runCommand</b>( {currentOp: 1} ) // if we want to retrieve the current operations being executed in MongoDB
db.<b>getCollectionNames()</b> // some commands have their own functions
</pre>
How to create views in MongoDB?
------------------------------------------------
<pre>
db.<b>createView</b>("short_movie_info",
              "movies",
              [ { $project: { "year": 1, "title":1, "plot":1}}]
)
</pre>
------------------------------------------------
3.QUERYING IN MONGODB
==============
<pre>
db.users.<b>find({"name":"David"})</b>
db.comments.<b>find()</b> // find all documents
db.comments.<b>findOne()</b>

var comments = db.comments.find()
comments.<b>next()</b>
comments.<b>hasNext()</b>

// All of the queries have the same behavior
db.comments.<b>find()</b>
db.comments.<b>find({})</b>
db.comments.<b>find({"a_non_existent_field" : null})</b>
</pre>

> Documents are returned in a cursor.
> Typing "it" every time will return the next set of 20 documents until the collection contains more elements.

<pre>
db.comments.<b>find</b>(
                 {"name" : "Lauren Carr"},
                 <b>{"name" : 1, "date": 1}</b> //Projection: Selecting the returned fields
)

db.movies.<b>distinct("rated")</b>
db.movies.<b>distinct</b>("rated", <b>{"year" : 1994}</b>) //all the unique ratings the films that were released in 1994

db.movies.<b>count()</b> // without a query it is based on collection's metadata
db.movies.<b>count({"num_mflix_comments" : 6})</b>
db.movies.<b>countDocuments({"year": 1999})</b> // query argument is mandatory
db.movies.<b>countDocuments({})</b> // to count all documents
db.movies.<b>estimatedDocumentCount()</b> // based on the collection's metadata

db.movies.<b>find({"num_mflix_comments" : 5})</b>
db.movies.<b>find</b>({ "num_mflix_comments" : <b>{$eq : 5 }</b>}) //returns movies that have exactly 5 comments

db.movies.<b>find</b>({year : <b>{$gt : 2015}</b>}).count()
db.movies.<b>find</b>({"released" :<b>{$gte: new Date('2000-01-01')}</b>}).count()

<b>$lt:</b>  less than
<b>$lte:</b> less than or equal to

db.movies.<b>find</b>({"rated" :<b>{$in : ["G", "PG", "PG-13"]}</b>})
db.movies.find({"rated" : <b>{$nin : ["G", "PG", "PG-13"]}</b>})
db.movies.<b>countDocuments</b>(<b>{"non_existing_field" :{$nin : ["a value", "another value", null ]}</b>}) // in mongodb non existent fields always has a value of null
</pre>

Logical Operators
------------------
<pre>
db.movies.<b>countDocuments</b>(
                    {<b>$and: [{"rated" : "UNRATED"}, {"year" : 2008}]</b>}
)

db.movies.<b>find</b>(
                {
                  <b>$or:[
	                     {"rated" : "G"},
	                     {"year" : 2005},
	                     {"num_mflix_comments" : {$gte : 5}}
]</b>}

<b>$nor:</b> The $nor operator is syntactically like $or but behaves in the opposite way.

db.movies.<b>find</b>(
              {
                 "num_mflix_comments" :<b>{$not : {$gte : 5} }</b>
              }
)

db.movies.<b>find</b>(
               {"title" : <b>{$regex :"Opera"}</b>}
)

db.movies.<b>find</b>(
	      {"title" : <b>{$regex :"^Opera"}</b>}  // that <b>starts</b> with Opera
)

db.movies.<b>find</b>(
	      {"title" : <b>{$regex :"Opera$"}</b>} //that <b>ends</b> with Opera
)

db.movies.<b>find</b>(
        {
           "title" :{<b>"$regex"</b> : "the", <b>$options: "i"</b>} // case insensitive option
        }
)

// if <b>array field</b> contains at least one element that satisfies the query
db.movies.<b>find</b>(
    {  $and :[
               {"cast" : "Charles Chaplin"},
               {"cast": "Edna Purviance"}
             ]
    }
)

// <b>querying with an array value:</b> Array values must match <b>exactly</b> (order etc.)
db.movies.<b>find</b>(
                <b>{"languages" : ["German", "English"]}</b>
)

// the <b>$all</b> operator finds all those documents where the value of the field contains all
// the elements, irrespective of their order or size:
db.movies.<b>find</b>(
              {
                "languages":{<b>"$all" :[ "English", "French", "Cantonese"]</b>}
              }
)
</pre>

How to project array fields in MongoDB?
--------------- 
<pre>
db.movies.find(
                {"languages" : "Syriac"}, 
                {"languages<b>.$</b>" :1}   // if more than one element is matched, the <b>$ operator projects only the first matching element</b>
)

db.movies.<b>find</b>({"title" : "Youth Without Youth"},
               {"languages" : <b>{$slice : 3}</b>} //first three elements of the array (you can query different field and still use this operator)
).<b>pretty()</b>

{"languages" : <b>{$slice : -2}</b>} // last two elements
{"languages" : <b>{$slice : [2, 4]</b>}} // skip first two elements and get four elements
</pre>

How to query nested objects in MongoDB?
---------------------- 
> This means that <b>all the field-value pairs, along with the order of the fields must match exactly.</b>
<pre>
db.movies.<b>find</b>(
               {"awards":
                        <b>{"wins": 1, "nominations": 0, "text": "1 win."}</b>
               }
)
</pre>

How to query by nested objects' fields in MongoDB?
-----------------------------
<pre>
db.movies.find(
                <b>{"awards.wins" : 4}</b>
)
</pre>

How to project nested objects' fields in MongoDB?
---------------------------------
<pre>
db.movies.find(
               {},
               {
                <b>"awards.wins" :1,
                "awards.nominations" : 1,
                "_id":0
                </b>
               }
)
</pre>

How to limit query result in MongoDB?
---------------------
<pre>
db.movies.find(
               {"cast" : "Charles Chaplin"},
               {"title": 1, "_id" :0}
).<b>limit(3)</b>

limit(0) //returns all
</pre>

> Different MongoDB drivers can have different batch sizes. However, for a single query, the batch size can be set.

<pre>
db.movies.find(
               {"cast" : "Charles Chaplin"},
               {"title": 1, "_id" :0}
).<b>batchSize(5)</b>
</pre>

> The skip() operation does not make use of any indexes, so it performs nicely on a smaller collection but may lag noticeably on larger collections.
<pre>
db.movies.<b>find</b>(
          {"cast" : "Charles Chaplin"},
		      {"title": 1, "_id" :0}
).<b>skip(2)</b>  

db.movies.<b>find</b>(
			      {"cast" : "Charles Chaplin"},
			      {"title" : 1, "_id" :0}
).<b>sort({"title" : 1}) //1:ascending -1:descending</b>
</pre>
------------------------------------

4.INSERT, UPDATE, DELETE OPERATIONS
==============

How to insert documents in MongoDB?
--------------------
<pre>
db.collection.<b>insert(<Document To Be Inserted>)</b>
db.new_movies.<b>insertMany([
                          {"_id" : 2, "title": "Baby Driver"},
                          {"_id" : 3, "title": "Logan"},
                          {"_id" : 4, "title": "John Wick: Chapter 2"},
                          {"_id" : 5, "title": "A Ghost Story"}
])</b>
</pre>

How to delete documents in MongoDB?
--------------------
<pre>
db.new_movies.<b>deleteOne({"_id": 2})</b>
db.new_movies.<b>deleteMany({"title" : {"$regex": "^movie"}})</b>

// Difference <b>findOneAndDelete</b> of from deleteOne: 
// [1] you can sort before delete,
// [2] returns deleted item
// [3] you can project deleted item

db.new_movies.<b>findOneAndDelete</b>( 
                            {"title" : {"$regex" : "^movie"}},
                            {<b>sort</b> : {"_id" : -1}}
)

db.new_movies.<b>findOneAndDelete</b>(
                            {"title" : {"$regex" : "^movie"}},
                            {
                              <b>sort</b> : {"_id" : -1}, 
                              <b>projection</b> : {"_id" : 0, "title" : 1}
                            }
)
</pre>

How to replace documents in MongoDB?
--------------------
<pre>
db.users.<b>replaceOne(
                    {"_id" : 5},
                    {"name": "Margaery Baratheon", "email": "Margaery.Baratheon@got.es"} // beware: no _id field in the updated document
)
</b>

// <b>upsert</b> option: insert if not found
db.users.<b>replaceOne</b>(
                {"name" : "Margaery Baratheon"},
                {"name": "Margaery Tyrell", "email": "Margaery.Tyrell@got.es"},
                { <b>upsert:</b> true }
)
</pre>

<pre>
db.movies.<b>findOneAndReplace</b>(
                                {"title" : "Macbeth"}, 
                                {"title" : "Macbeth", "latest" : true},
                                {
                                <b>sort:</b> {"_id" : -1},
                                <b>projection:</b> {"_id" : 0},
                                <b>returnNewDocument:</b> true
                                }
)
</pre>

**Its effect is equal to:**

<pre>
<b>var deletedDocument</b> = db.movies.<b>findOneAndDelete</b>(
                                                      {"title" : "Macbeth"},
                                                      {sort : {"_id" : -1}}
                                                  );
db.movies.<b>insert</b>(
                 {"_id" : deletedDocument._id, "title" : "Macbeth", "latest" : true}
)
</pre>

How to update documents in MongoDB?
--------------------
<pre>
db.movies.<b>updateOne</b>(
                    {"title" : "Macbeth"},
                    {<b>$set :</b> {"year" : 2015}}
)

// can be used to update multiple fields in the same command,
// and if a field is new, it will be added to the document
db.movies.<b>updateOne</b>(
                    {"title" : "Macbeth"},
                    { <b>$set :</b> {"type" : "movie", "num_mflix_comments" : 1}}
)

//If not found it is inserted and and it will even have title field.
db.movies.<b>updateOne</b>(
                    {"title" : "Sicario"},
                    {$set : {"year" : 2015}},
                    {"upsert" : true}
)

// <b>by default it returns the old document</b>
db.movies.<b>findOneAndUpdate</b>(
                          {"title" : "Macbeth"},
                          { <b>$set :</b> {"num_mflix_comments" : 10}}
)

db.movies.<b>findOneAndUpdate</b>(
                           {"title" : "Macbeth"},
                           {<b>$set :</b> {"num_mflix_comments" : 15}},
                           {
                             <b>"projection" :</b> {"_id" : 0, "num_mflix_comments" : 1},
                             <b>"returnNewDocument" :</b> true,
					                   <b>"sort" :</b> {"_id" : -1}
                           }
)
</pre>

Some update operators in MongoDB
--------------------------------
### Set ($set):
<pre>
db.movies.<b>updateMany</b>(
                     {"year" : 2015},
                     {<b>$set :</b> {"languages" : ["English"]}}
)
</pre>

### Increment ($inc):
<pre>
db.movies.<b>findOneAndUpdate</b>(
                           {"title" : "Macbeth"},
                           {<b>$inc :</b> {"num_mflix_comments" : 3, "rating" : 1.5}},
                           {returnNewDocument : true}
)
</pre>

### Multiply ($mul):
<pre>
// When using a non-existent field with $mul, we should always remember that no matter what
// multiplier we provide, the field will be created and always set to zero
db.movies.<b>findOneAndUpdate</b>(
                           {"title" : "Macbeth"},
                           {<b>$mul :</b> {"rating" : 2}},
                           {returnNewDocument : true}
)
</pre>

### Rename ($rename):
> The provided field and its new name must be different. 
> if they're the same, the operation fails with an error
<pre>
db.movies.<b>findOneAndUpdate</b>(
                           {"title" : "Macbeth"},
                           {<b>$rename :</b> {"num_mflix_comments" : "comments",
                                       "imdb_rating" : "rating"}},
                           {returnNewDocument : true}
)

// a field can be moved to nested document.
db.movies.<b>findOneAndUpdate</b>(
                            {"title" : "Macbeth"},
                            {<b>$rename :</b> {"rating" : <b>"imdb.rating"</b>}},
                            {returnNewDocument : true}
)
</pre>

### Current Date ($currentDate):
<pre>
db.movies.<b>findOneAndUpdate</b>(
                           {"title" : "Macbeth"},
                           {<b>$currentDate :</b> {
                                             "created_date" : true, // defaults type to Date type
                                             "last_updated.date" : <b>{$type : "date"}</b>,
                                             "last_updated.timestamp" : <b>{$type : "timestamp"}</b>,
                                           }},
                           {returnNewDocument : true}
)
</pre>

### Removing Fields ($unset):
<pre>
//values have no effect
db.movies.<b>findOneAndUpdate</b>(
                         {"title" : "Macbeth"},
                         {
                          <b>$unset :</b>{
                                    "created_date" : "",
                                    "last_updated" : "dummy_value",
                                    "box_office_collection": 142.2,
                                    "imdb" : null,
                                    "flag" : ""
                                          }
                         },
                         {returnNewDocument : true}
)
</pre>

### Setting When Inserted ($setOnInsert):
<pre>
db.movies.<b>findOneAndUpdate</b>(
                          {"title":"Macbeth"},
                          {
                             $rename:{"comments":"num_mflix_comments"},
                          <b>$setOnInsert:</b>{"created_time":new Date()}
                          },
                          {
                            upsert : true,
                            returnNewDocument:true
                          }
)
</pre>
------------------------

5.UPDATING WITH AGGREGATION PIPELINE AND ARRAYS
=================
### Aggregation Pipeline Example
<pre>
db.users.<b>updateMany</b>( {},
                     [
                      {<b>$set :</b> {"name_array" : {<b>$split :</b> [<b>"$full_name"</b>, " "]}},},
                      {
                        <b>$set:</b> {
                               "first_name" : {<b>"$arrayElemAt" : ["$name_array", 0]</b>},
                               "last_name" : {<b>"$arrayElemAt" : ["$name_array", 1]</b>}
                               }
                      },
                      {
                         <b>$project :</b> {
                                     "first_name" : 1,
                                     "last_name" : 1,
                                     "full_name" : {
                                                     <b>$concat :</b> [<b>{$toUpper : "$first_name"}</b>, " ", "$last_name"]
                                                   }
                                    }
                      }
                     ]
)
</pre>

How to push/add a single element to an array field in MongoDB?
--------------------
<pre>
db.movies.<b>findOneAndUpdate</b>(
                           {_id : 111},
                           {<b>$push :</b> {"genre" : "unknown"}},
                           {"returnNewDocument" : true}
)
</pre>

How to push/add a multiple elements to an array field in MongoDB?
--------------------
<pre>
db.movies.<b>findOneAndUpdate</b>(
                           {_id : 111},
                           {<b>$push :</b> {
                                     "genre" : {
                                                <b>$each :</b> ["History", "Action"]
                                               }
                                    }
                           },
                           {"returnNewDocument" : true}
)

<b>// you can sort an array field, after pushing an item</b>
db.movies.<b>findOneAndUpdate</b>(
                           {_id : 111},
                           {<b>$push :</b> {
                                     "genre" : {
                                                $each : [],
                                                <b>$sort : 1</b> 
                                                }
                                    }
                           },
                           {"returnNewDocument" : true}
)

<b>// you can sort using propepties of array items</b>
db.items.<b>findOneAndUpdate</b>(
                          {_id : 11},
                          {<b>$push :</b> {
                                    "items" : {
                                                $each : [],
                                                <b>$sort : {"price" : -1}</b>
                                              } 
                                   }
                          },
                          {"returnNewDocument" : true}
)
</pre>

How to add only unique items to an array field in MongoDB? (as a set)
-------------------
<pre>
db.movies.<b>findOneAndUpdate</b>(
                           {_id : 111},
                           {<b>$addToSet :</b> {"genre" : "Action"}},
                           {"returnNewDocument" : true }
)
</pre>

How to delete first/last element of an array field in MongoDB?
-------------------
<pre>
db.movies.<b>findOneAndUpdate</b>(
                           {_id : 111},
                           {<b>$pop :</b>{"genre" : <b>1</b>}}, //1:removes <b>last</b> element, -1: removes <b>first</b> element
                           {"returnNewDocument" : true }
)
</pre>

How to delete all elements with certain values from an array field in MongoDB?
-------------------
<pre>
db.movies.<b>findOneAndUpdate</b>(
                           {_id : 111},
                           {<b>$pullAll :</b> {"genre" : ["Action", "Crime"]}},
                           {"returnNewDocument" : true }
)
</pre>

How to delete elements of an array by querying their subproperties from an array field in MongoDB?
-------------------
<pre>
db.items.<b>findOneAndUpdate</b>(
                          {_id : 11},
                          { <b>$pull :</b> {
                                     "items" : <b>{
                                                 "quantity" : 3,
                                                 "name" : {$regex: "ck$"}
                                               }</b>
                                    }
                          },
                          {"returnNewDocument" : true }
)
</pre>

How to set all elements of an array field in MongoDB?
-------------------
<pre>
db.movies.<b>findOneAndUpdate</b>(
                          {_id : 111},
                          {<b>$set :</b> {<b>"genre.$[]"</b> : "Action"}},
                          {"returnNewDocument" : true}
)
</pre>

How to update specific array items using filters in MongoDB?
-------------------
> By using <b>arrayFilters</b> keyword define the query condition (myElements).
<pre>
db.items.<b>findOneAndUpdate</b>(
                          {_id : 11},
                          {<b>$set : </b>{
                                   "items<b>.$[myElements]</b>" : {
                                                            "quantity" : 7,
                                                            "price" : 4.5,
                                                            "name" : "marker"
                                                           }
                                  }
                          },
                          {
                            "returnNewDocument" : true,
                            <b>"arrayFilters" : [{"myElements.quantity" : null}]</b>
                          }
)
</pre>
---------------------------

6.DATA AGGREGATION
============================
#### Syntax:
<pre>
use sample_mflix;
var pipeline = [] // the pipeline is an array of stages.
var options = {} 
var cursor = db.movies.<b>aggregate(pipeline, options);</b>
</pre>

#### Example 1:
<pre>
var pipeline = [
                { <b>$match:</b> { "location.address.state": "MN"} },
                { <b>$project:</b> { "location.address.city": 1 } },
                { <b>$sort:</b> { "location.address.city": 1 } },
                { <b>$limit:</b> 3 }
];
db.theaters.<b>aggregate(pipeline)</b>
db.theaters.<b>aggregate(pipeline).forEach(printjson);</b>
</pre>

#### Example 2:
<pre>
var pipeline = [
                { <b>$match:</b> {genres: "Romance", released: {<b>$lte: new ISODate("2001-01-01T00:00:00Z") </b>}}},
                { <b>$sort:</b> {"imdb.rating": -1}}, 
                { <b>$limit:</b> 3 },
];
db.movies.aggregate(pipeline).forEach(printjson);
</pre>

How to group(aggregation) query result in MongoDB? 
---------------- 
> In aggregation terms, an expression can be a literal, an expression object, an operator,or a field path.
<pre>
// The $group stage will interpret _id: "$rated" as equivalent to _id: "$$CURRENT.rated".
var pipeline = [
                {<b>$group:</b> {
                          _id: <b>"$rated"</b> //field path example
                         }
                }
               ];
</pre>               

Accumulator expressions in MongoDB
------------------------
#### $sum
<pre>
var pipeline = [
                {<b>$group:</b> {
                           _id: "$rated",
                           "numTitles": { <b>$sum: 1</b>},
                         }
                }
];
db.movies.aggregate(pipeline).forEach(printjson);
</pre>

#### $avg
<pre>
{$group: {
           _id: "$rated",
           "avgRuntime": { <b>$avg:</b> "$runtime"},
}}

// $trunc stage to give integer value
{$project: {
            "roundedAvgRuntime": { <b>$trunc:</b> "$avgRuntime"}
}}
</pre>

#### Example 3:
<pre>
var pipeline = [
                {<b>$match:</b> {released: {$lte: new ISODate("2001-01-01T00:00:00Z") }
                          }
                },
                {<b>$group:</b> {
                      _id: {<b>"$arrayElemAt": ["$genres", 0]</b>},
                      "popularity": { <b>$avg:</b> "$imdb.rating"},
                      "top_movie": { <b>$max:</b> "$imdb.rating"},
                      "longest_runtime": { <b>$max:</b> "$runtime"}
                     }
                },
                { <b>$sort:</b> { popularity: -1}},
                { <b>$project:</b> {
                         _id: 1,
                         popularity: 1,
                         top_movie: 1,
                         adjusted_runtime: { <b>$add:</b> [ "$longest_runtime",12 ] // adjusted_runtime = longest_runtime + 12(trailer length)
                       } 
                 } 
                }
];
</pre>

#### $first (to choose first element of the group)
<pre>
{ <b>$group:</b> {
           _id: {"$arrayElemAt": ["$genres", 0]},
           "recommended_title": {<b>$first:</b> "$title"},
           "recommended_rating": {<b>$first:</b> "$imdb.rating"},
           "recommended_raw_runtime": {<b>$first:</b> "$runtime"},
           "popularity": { $avg: "$imdb.rating"},
           "top_movie": { $max: "$imdb.rating"},
           "longest_runtime": { <b>$max:</b> "$runtime"}
          }
},
</pre>

How to randomly sample a certain amount of documents in MongoDB?
----------------
> <b>$limit</b> always respects the order of the documents and thus returns the same documents every time.
<pre>
{ <b>$sample:</b> {size: 100}}, // This will reduce the scope to 100 random docs.
</pre>

How to join two collections in MongoDB?
----------------
> <b>$lookup</b> creates a new array field filled with related documents.
<pre>
var pipeline = [
                { $match: { $or: [{"name": "Catelyn Stark"},{"name": "Ned Stark"}]}},
                { <b>$lookup:</b> {
                             <b>from:</b> "comments", // collection to join
                             <b>localField:</b> "name", // the field to link in original collection
                             <b>foreignField:</b> "name", // the field to link from "from" collection
                             <b>as:</b> "comments" // the new field name in the result
                            }
                },
                { $limit: 2},
];
db.users.aggregate(pipeline).forEach(printjson);
</pre>

> Using <b>$unwind</b> operator, you can deconstruct the array field and  
> create a new document for each element in the array field.

**$unwind** converts this:  
{a: 1, b: 2, c: [1, 2, 3, 4]}    
  
To this:  
{"a" : 1, "b" : 2, "c" : 1 }  
{"a" : 1, "b" : 2, "c" : 2 }  
{"a" : 1, "b" : 2, "c" : 3 }  
{"a" : 1, "b" : 2, "c" : 4 }  

<pre>
{ <b>$unwind:</b> "$comments"}
</pre>

How to save output of a query pipeline into another collection in MongoDB?
----------------------
> By using the <b>$out</b> operator in a stage. It takes the name of desired output collection.  
> It will either create a new collection or completely replace an existing collection.  
> $out must output to the same database as the aggregation target.
<pre>
{ <b>$out:</b> "myOutputCollection"}
</pre>

> From Mongo 4.2 also $merge is available.  
> The <b>$merge</b> operator must be the last stage in the pipeline.  
> You can merge into a collection which is in another database.

#### Syntax:
<pre>
{ <b>$merge:</b> {
     <b>into:</b> <collection> -or- { db: <db>, coll: <collection> },
     <b>on:</b> <identifier field> -or- [ <identifier field1>, ...],// Optional
     <b>let:</b> <variables>,// Optional
     <b>whenMatched:</b> <replace|keepExisting|merge|fail|pipeline>,  // Optional
     <b>whenNotMatched:</b> <insert|discard|fail>// Optional
} }
</pre>

### A pipeline example with all operators so far:
<pre>
var pipeline = [
                { <b>$sample:</b> {size: 5000}},
                { <b>$group:</b> {
                           <b>_id:</b> "$movie_id",
                           "sumComments": <b>{ $sum: 1}</b>
                          }
                },
               { <b>$sort:</b> { "sumComments": -1}},
               { <b>$limit:</b> 5},
               { <b>$lookup:</b> {
                            <b>from:</b> "movies",
                            <b>localField:</b> "_id",
                            <b>foreignField:</b> "_id",
                            <b>as:</b> "movie"
                          }
               },
               {<b>$unwind:</b> "$movie" },
               {<b>$project:</b> {
                             "movie.title": 1,
                             "movie.imdb.rating": 1,
                             "sumComments": 1,
                           }
               },
               {<b>$out:</b> "most_commented_movies" }
];
</pre>

### Some aggreagate options:
> Pass options object to aggregate like below:  
> db.movies.<b>aggregate</b>(pipeline, <b>options</b>);

* <b>maxTimeMS:</b> The amount of time an operation may be processed before MongoDB kills it.
* <b>allowDiskUse:</b> true means using disk is allowed to handle massive datasets
* <b>bypassDocumentValidation:</b> No validation of output when using $out or $merge
* <b>comment:</b> This options is for debugging allows a string to be specified

<pre>
var options = {
               <b>maxTimeMS:</b> 30000,
               <b>allowDiskUse:</b> true
}
</pre>
-----------------------------

6.INDEXES IN MONGODB
============================
<pre>
db.movies.explain().find(
                          {
                           "year" : 2015
                          },
                          {
                           "title" : 1,
                           "awards.wins" : 1
                          }
                         ).sort({"awards.wins" : -1})
</pre>

> **explain()** function can take an optional parameter:verbosityMode

**Possible Values:**
* "queryPlanner": default option, print query planner detail, rejected plans, winning plan, execution stagesof winning plan 
* "executionStats": Useful for finding performance related problems
* "allPlansExecution": 

How to create index in MongoDB?
---------------------
<pre>
db.movies.<b>createIndex</b>(
                      {year: 1} //1 is ascending
)
</pre>

How to list indexes on a collection in MongoDB?
---------------------
<pre>
db.movies.<b>getIndexes()</b>
</pre>

How to give a specific name to an index in MongoDB?
---------------------
<pre>
db.theaters.<b>createIndex</b>( 
                        {theaterId : -1},
                        <b>{name : "myTheaterIdIndex"}</b>
);
</pre>

How to create an index with multiple fields in MongoDB?
---------------------
<pre>
db.movies.<b>createIndex</b>(
                      <b>{year : 1, rated : 1}</b> //compound index
)
</pre>

How to create an index on nested document's fields in MongoDB?
---------------------
<pre>
db.theaters.<b>createIndex</b>(
                        { <b>"location.address.zipcode" :</b> 1}
)
</pre>

How to create an index on the embedded document (not on its fields) in MongoDB?
---------------------
<pre>
db.theaters.<b>createIndex</b>(
                        { "location" : 1}
)
</pre>

How to create an index with wildcards in MongoDB?
---------------------
> Creating indexes with wildcars is useful for indexing "to be inserted-future" fields.
<pre>
db.products.<b>createIndex</b>(
                        { "specifications<b>.$**</b>" : 1}
)
</pre>

How to create an index with wildcards for all fields in MongoDB?
---------------------
<pre>
db.products.<b>createIndex</b>(
                        <b>{ "$**" : 1 }</b>
)
</pre>

How to omit specific fields for indexing in MongoDB?
---------------------
<pre>
db.products.<b>createIndex</b>(
                         { "$**" : 1 },
                         {
                           <b>"wildcardProjection" : { "name" : 0 }</b>
                         }
)
</pre>

How to create a unique index in MongoDB?
---------------------
<pre>
db.collection.<b>createIndex</b>(
                          { field: type},
                          <b>{ unique: true }</b>
)
</pre>

How to make sure that documets are deleted after a certain amount of time?
---------------------
> By using Time to live (TTL) indexes!  
> The document (not the index itself) will be deleted after a time.  
> The index can be defined only on a field of date type
<pre>
db.collection.<b>createIndex</b>({ field: type}, <b>{ expireAfterSeconds: seconds }</b>)
</pre>


//A sparse index will not have entries from the collection where the indexed field does not exist
db.collection.createIndex({ field1 : type, field2: type2, ...}, { sparse:
true })

How to create an index only for some documents in a collection in MongoDB?
-------------------------
> By using <b>partial indices</b>  
> A partial index is an index for documents that satisfy some condition.
db.movies.<b>createIndex</b>(
                      {title: 1, type:1},
                      <b>{
                        partialFilterExpression: {year : { $gt: 1950}}
                      }</b>
)

How to create a case insensitive index in MongoDB?
----------------------
<pre>
db.movies.<b>createIndex</b>(
                       {title: 1},
                       <b>{
                         collation: {locale: 'en', strength: 2}
                       }</b>
)
</pre>

How to drop an index on a collection in MongoDB?
---------------------
<pre>
db.collection.<b>dropIndex(indexNameOrSpecification);</b>
</pre>

How to drop all indexes on a collection in MongoDB?
---------------------
<pre>
db.collection.<b>dropIndexes();</b> //drops all indexes except default _id index
</pre>

How to analyze index performance without dropping it in MongoDB?
---------------------
<pre>
db.collection.<b>hideIndex(indexNameOrSpecification)</b>; //to analyze performance impact without removing
db.movies.<b>explain("executionStats")</b>.find()
db.collection.<b>unhideIndex(indexNameOrSpecification)</b>;
db.movies.<b>explain("executionStats")</b>.find()
</pre>

What is the best way to create index for text search queries in MongoDB?
--------------------
> A collection can have <b>at most one</b> text index.  
> Text index can be defined on text fields and arrays with texts.
<pre>
db.users.<b>createIndex</b>(
                     { name : <b>"text"</b>}
)
</pre>

How to give hint to MongoDB for using a specific index?
------------------
<pre>
db.users.find().<b>hint({ index })</b>
</pre>
---------------------------

7.MongoDB REDUNDANCY
===================

How to get the size of Oplog in MongoDB?
---------------------
> The value is in bytes
<pre>
db.<b>oplog.rs.stats().maxSize</b>
</pre>

How to list cluster members in MongoDB?
---------------------
<pre>
rs.<b>status().members</b>
</pre>

How to learn connection information in MongoDB?
--------------------
> You can learn if you connected to a replica set or not.  
> Replica set connections is shown like: [replicaset name]/server1:port1, server2:port2,... 

<pre>
db.<b>getMongo()</b>
</pre>

How to enable read operations on secondary nodes using mongoshell in MongoDB?
--------------------
<pre>
rs.<b>slaveOk()</b>
</pre>

How to get&set read preference of a cluster in MongoDB?
-------------------------
<pre>
db.<b>getMongo().getReadPrefMode()</b>

db.<b>getMongo().setReadPref('primaryPreffered')</b>
</pre>

#### Read Preference Options:

* null(default)
* primary
* primaryPreferred
* secondary
* secondaryPreferred
* nearest

How to set write concern option in write operations in MongoDB?
-------------------------
<pre>
db.new_collection.<b>insert</b>({"_id":1, "info": "test writes"},<b>{w:2}</b>)
</pre>

How to add a node to a replica set in MongoDB?
---------------------
<pre>
rs.<b>add({host: "node4.domain.com:27017", hidden : true,votes : 1})</b>//adds a hidden node
</pre>

How to remove a node from a replica set in MongoDB?
---------------------
> Removing a node from replica set do not delete data files and the node instance.

<pre>
rs.<b>remove({ <hostname.com> })</b>//adds a hidden node
</pre>

How to configure cluster in MongoDB?
---------------------
> The range for priority is between 0 and 1000 (inclusive).  
> Default priority value is 0.  
> Instances with 0 priority can not be primary!  

<pre>
var conf = <b>rs.config()</b>
<b>conf.members[0].priority=1</b>
conf.members[1].priority=0.5
conf.members[2].priority=0
rs.<b>reconfig(conf)</b>
</pre>

How to transfer primary state in a cluster to another node in MongoDB?
--------------
<pre>
rs.<b>stepDown()</b>
</pre>

How to verify connected node is primary in MongoDB?
-------------
<pre>
rs.<b>isMaster()</b>
</pre>

How to shutdown an instance in MongoDB?
--------------
<pre>
db.<b>shutdownServer()</b>
</pre>

What are the node types in a cluster in MongoDB?
--------------------

* **PRIMARY:** Used for read write operations.
* **SECONDARY:** Used for read operations, replicates from primary servers Oplog records.
* **READ ONLY:** Secondary and can never become primary 
* **ARBITER:** No replication so no data, the purpose of existence is to vote in elections
* **HIDDEN:** Not for read operations, for datawarehousing applications 
* **DELAY:** Used to obtain older snapshots of primary