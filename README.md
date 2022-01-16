# A Simple Website with React, Axios and a REST API Built with MongoDB Realm

This project is a template and a proof of concept to showcase the technologies mentioned above.

https://www.mongodb.com/developer/how-to/react-query-rest-api-realm/

https://github.com/mongodb-developer/mongodb-realm-react-rest-api

# Associated blog post

Please read this associated [blog post](https://www.mongodb.com/developer/how-to/react-query-rest-api-realm/) to get all the details about this project.

# Start the Beast

It's as simple as cloning this repository and then

```
npm start
```

The website will then be available at [http://localhost:3000/](http://localhost:3000/).

# Author

Maxime Beugnet <maxime@mongodb.com>

# Questions?

If you have any questions or comments, please create a topic in our [community forum](https://www.mongodb.com/community/forums/) and ping me: [@MaBeuLux88](https://www.mongodb.com/community/forums/u/mabeulux88/summary).


Introduction
REST APIs and React are both very popular technologies in their respective fields and they are also very interesting to combine in a project.

In this blog post, I will show you how we can use a REST API that I created with the help of MongoDB Realm to access my data in my MongoDB Atlas cluster and display the result of the query in a website built with React.

At the end of this tutorial, you will have a basic React project capable of retrieving a list of countries through a REST API and displaying them on a web page.

In the next blog post, I will use this project as a starting point. The list of countries will become a filter for a bunch of charts built with MongoDB Charts that I will be able to import into a website using the MongoDB Charts SDK. The final result will be a COVID-19 dashboard which will be easy to filter by country.

Prerequisites
To build this project, you will need a recent version of NodeJS.

Please check that your versions of node, npm, and npx are close to my versions:

$ node -v
v14.17.1
$ npm -v
7.20.6
$ npx -v
7.20.6

copy code
In the body of this blog post, I will step through how to build this app. If you don't want to follow along, and you prefer to jump to the final project, you can clone this repository from Github:

git clone git@github.com:mongodb-developer/mongodb-realm-react-rest-api.git
cd mongodb-realm-react-rest-api
npm start

copy code
Create a React Application
npx create-react-app mongodb-realm-react-rest-api
cd mongodb-realm-react-rest-api
npm start

copy code
The last command should automatically open a new tab in your favorite browser at the address http://localhost:3000, and you should see the default React spinning logo. Before we continue, we can simplify the project by removing all the files we don't need.

rm -f public/logo* public/manifest.json public/robots.txt src/* yarn.lock

copy code
We now have a clean canvas for the next step.

Install Axios
Axios is a promise-based HTTP client for Node.js and the most famous HTTP client, as far as I know, with currently more than 14 million weekly downloads. We will use it to query our REST API and retrieve the list of countries to display.

npm i axios

copy code
Checking the REST API
As mentioned in the introduction, the REST API we are going to use in this blog post already exists and was created using MongoDB Realm so it can scale easily and automatically. This REST API exposes metadata from the COVID-19 Open Data Cluster that we made publicly available. Check out this blog post for all the details.

This is the REST API we will use. You can open it in a new tab in your browser or use the curl command:

curl https://webhooks.mongodb-stitch.com/api/client/v2.0/app/covid-19-qppza/service/REST-API/incoming_webhook/metadata

copy code
The result from accessing this REST API is a JSON document that looks like this:

{
  _id : "metadata",
  countries : [ "Afghanistan", "Albania", "Algeria", "..." ],
  states : [ "Alabama", "Alaska", "Alberta", "..." ],
  states_us : [ "Alabama", "Alaska", "American Samoa", "..." ],
  counties : [ "Abbeville", "Acadia", "Accomack", "..." ],
  iso3s : [ "ABW", "AFG", "AGO", "..." ],
  uids : [ 4, 8, 12, ... ],
  first_date : 2020-01-22T00:00:00.000+00:00,
  last_date : 2021-08-17T00:00:00.000+00:00
}

copy code
And it contains the list of countries that I want, which is the list of all the countries that exist in the Johns Hopkins University data set.

Now that we have everything we need to build our React website, let's code!

Building the Website
If you followed all the instructions so far, here is what your working folder should look like:

.
├── package.json
├── package-lock.json
├── public
│   ├── favicon.ico
│   └── index.html
├── README.md
└── src

copy code
The current favicon is the React logo. You can totally replace it with the MongoDB one 😎.

Let's now edit the contents of public/index.html. Nothing really fancy compared to the original version that came with the sample application. I just removed what wasn't necessary.

<!DOCTYPE html>
<html lang="en">
<head>
  <meta charset="utf-8"/>
  <link rel="icon" href="%PUBLIC_URL%/favicon.ico"/>
  <meta name="robots" content="noindex" />
  <meta name="viewport" content="width=device-width, initial-scale=1"/>
  <meta name="theme-color" content="#000000"/>
  <meta name="description" content="Demo Application using MongoDB, React and Axios."/>
  <title>MongoDB React REST API</title>
</head>
<body>
<noscript>You need to enable JavaScript to run this app.</noscript>
<div id="root"></div>
</body>
</html>

copy code
The only thing that we need to notice in this file is the fact that we now have an empty div with an id root which we will use to inject our React content.

Let's create a new file src/index.js:

import React from 'react';
import ReactDOM from 'react-dom';
import RestExample from "./RestExample";
ReactDOM.render(<React.StrictMode>
  <RestExample/>
</React.StrictMode>, document.getElementById('root'));

copy code
We are now injecting our React content into our website and the content will be what the new RestExample component will render.

Let's create this new function component in the file src/RestExample.js:

import {useEffect, useState} from "react";
import axios from "axios";
const RestExample = () => {
  const url = 'https://webhooks.mongodb-stitch.com/api/client/v2.0/app/covid-19-qppza/service/REST-API/incoming_webhook/metadata';
  const [countries, setCountries] = useState([]);
  useEffect(() => {
    axios.get(url).then(res => {
      setCountries(res.data.countries);
    })
  }, [])
  return <div className="App">
    <h1>List of Countries</h1>
    <div>
      <ul>
        {countries.map(c => <li key={c}>{c}</li>)}
      </ul>
    </div>
  </div>
};
export default RestExample;

copy code
You can now start the project with the command:

npm start

copy code
There are a few things that are worth explaining here if you are new to React:

On line 6, we are creating a state, countries, that is initialised to an empty array and a function setCountries() that can alter the value of that variable. More documentation here.
With the help of the useEffect() function (starting at line 8) and the Axios library, we can execute the function once, extract the countries from the response, and set the countries values from our list. More documentation here.
Finally, our function component will render the returned content. The countries.map() function iterates over the list of countries and transforms each of the strings into an <li> tag to create an HTML unordered list.
Final Result
The final website should look like this:

Final result of the website with the list of countries.
If you got confused at any point, and your app isn't working, just clone the final project:

git clone git@github.com:mongodb-developer/mongodb-realm-react-rest-api.git
cd mongodb-realm-react-rest-api
npm start

copy code
Summary
In this blog post, you learned how to create a basic React website that uses Axios to retrieve data from a MongoDB Atlas Cluster using a REST API implemented with MongoDB Realm.

In the next blog post in this series, we'll extend this project so that we can integrate some charts from MongoDB Charts. We'll show COVID-19 data for a country and select which country we want to filter by. Stay tuned!

