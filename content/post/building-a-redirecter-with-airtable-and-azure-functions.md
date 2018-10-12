+++
date = "10/06/2017 10:00:00 +00:00"
tags = ["C#","Azure","Azure Functions"]
title = "Building a \"redirector\" with Airtable and Azure Functions"
+++
Once in a while I want a simple url alias. Say I want the link to my Stack
Overflow profile, but I want a simple, short url that's easy to remember. I want
an easy way to register the complex and specify a simple and short alias url
that redirects to my Stack Overflow profile.

That's what we're going to build here.

There are two parts to the application. The first part is the storage: I need to
store the url aliases somewhere. I've opted for Airtable. Airtable is simple
online table storage; it's like a light weight version of Excel. Airtable also
have a nice JSON API to access the tables in your account.

The second part is the code itself. This is basically just a simple handler for
incoming requests. Take the path after the domain and look it up in the table of
aliases. If there's a match, redirect to the specified url. For this part I've
chosen to use Azure Functions.

With Azure Functions you only pay for each execution of the function, but the
first 1 million executions each month are free(!). That means it's free for my
small use case.

As with Azure Web Apps (Azure Functions is just a Web App under the covers), a
function is turned off when it's not used. In contrast with a web app the
function is quite fast to spin up. A web app might take 10-20 seconds to spin
up, but the function responds in just 2-5 seconds. This makes the user
experience better, as they don't have to wait as long for the redirect.

Let's set things up:

Airtable
Go register an account at Airtable.com [https://airtable.com/]. First we create
a team. A team can contain multiple users which have access to the same
resources in Airtable. We use it organize our bases. I've created a team called 
blog.jonstodle.com  for demonstration purposes.

Next we create a base. A base is like an Excel document, it can contain multiple
tables of data. Click the plus icon and select Start from scratch. I've called
the base Redirects.



Click on the icon to open the base. Right click on the table tab (where it says 
Table 1) and rename it to Paths. Next we'll define three columns with the
following types. Right click the existing column headers and click on Customize
field type  to change the name and field type:

Content type
 Name
 Single line text
 Path
 URL
 Url
 Single line text
 Description
 I've also added some data to work with.



To have a look at how to access the data through the API, click on the question
mark in the upper right corner and click on API documentation.



On this page you'll be able to see the API key (by toggling show API key  in the
upper right corner) and the REST endpoints to get the data from the base. There
are some nice examples of responses you'll get from the API, with live data from
your actual base.

The important thing wee need here is the base url (https://api.airtable.com) and
the id of the base (the part after v0  in the path). In my case the endpoint to
my base is https://api.airtable.com/v0/appxpeLm4zw7rVbMV.

Note down the API key and the endpoint to your base for later.

That's everything we need from Airtable, so let's head over to the Azure
Function we're going to use.

Azure Function
Launch the Azure Portal [https://portal.azure.com/]  and click the plus sign in
the sidebar. Under the Compute  category, you'll find Function App. Fill out the
fields, just make sure Hosting Plan  is set to Consumption Plan. Below's how I
filled it out.



Click on Create  and Azure will deploy a new function app for you. After it's
done deploying, you'll be able to find the function app in the sidebar under 
More Services  > Function Apps.

Select your function app in the list, and you'll be presented with this:



Storing the API key
It's not good practice to store API keys directly in your code, so we'll add it
as an environment variable. That way it get's stored in a secure way by Azure
and we can get the key at run time.

Click on Application settings  under Configured features. Under Application
settings, click on Add new setting  and give it the name AirtableApiKey  and
paste the API key into the value field.



Creating the function
Click on the blue plus icon on the Functions  node to the right and click on 
Custom function, hidden below Get started on your own. Click on HttpTrigger - C#
, name you function Redirect  and set Authorization level  to Anonymous. Click
on Create.



You'll be met with a text editor containing the contents of run.csx, a C#
script. Remove all the code, we're going to do our own thing. Before we start
adding code however, we need to specify the route template.

Configuring the route
Click on Integrate  below the HttpRedirect  function in the sidebar. Set Allowed
HTTP methods  to Selected methods  and remove the check mark from all but GET 
in the Selected HTTP methods  section that appears. This ensures only HTTP GET 
requests are accepted. Not terribly important, but it's a good habit.

Next, set the Route template  to {path}. This specifies we want to capture the
path after the domain and store it in a variable called path. path  will also be
the name of the parameter that's passed into our handler.



Removing the /api  part of the path
By default you can access your function by going to 
<appname>.azurewebsites.net/api/, but we don't want the /api  part of the url.

This can be removed by editing the host.json  file of the app. The easiest way
for us to edit this is to go to <appname>.scm.azurewebsites.net  and click on 
Debug console  > CMD. Then navigate to site  > wwwroot  and click on the pencil
icon next to the file host.json. Inside this file, add the following JSON:

{
    "http": {
        "routePrefix": ""
    }
}


Then click on Save  to the upper left.

The function code
Let's go back to the function app in the Azure Portal and click on HttpRedirect 
in the sidebar. We'll start adding the code to handle the requests:

We'll reference Json.NET  to make handling the JSON responses from Airtable
easy.

#r "Newtonsoft.Json"


Then we add the necessary using  statements.

using static System.Environment;
using System.Net.Http;
using System.Net.Http.Headers;
using System.Net;
using Newtonsoft.Json;
using System.Threading.Tasks;


We'll also add some model classes to represent the responses from Airtable. The 
Record  class represents a row in our Airtable table. It contains some metadata
about the record, while the data we entered in the table is stored in the Fields 
 property.

public class Record
{
    public string Id { get; set; }
    public Redirect Fields { get; set; }
    public DateTimeOffset CreatedTime { get; set; }
}


The Redirect  class represents the data we entered in the table.

public class Redirect
{
    public string Path { get; set; }
    public string Url { get; set; }
    public string Description { get; set; }
}


Next, it's the Run  function. This will be executed when someone navigates to
our function app.

he part that's interesting to us is the path  parameter. This will contain the
path the visitor navigated to. If the user navigates to 
<appname>.azurewebsites.net/githubt, the path  parameter will contain the value 
github.

public static async Task<HttpResponseMessage> Run(HttpRequestMessage req, string path, TraceWriter log)
{


We start by creating an instance of HttpClient  and specify the base url.

    var client = new HttpClient
    {
        BaseAddress = new Uri("https://api.airtable.com/")
    };


We set the Authorization  header to contain the API key we got from Airtable. We
retrieve the key from the environment variable we set up earlier.

    client.DefaultRequestHeaders.Authorization = new AuthenticationHeaderValue("Bearer", GetEnvironmentVariable("AirtableApiKey"));


Next we retrieve the correct record from the Airtable table. We access the base
using the id we got from the API documentation, we then specify we want to
access the Paths  table in the base. Lastly we specify a filter to to only
receive the records we want.

You can read about the filter syntax in the Airtable Docs
[https://support.airtable.com/hc/en-us/articles/203255215-Formula-Field-Reference]

    var response = await client.GetStringAsync($"/v0/appxpeLm4zw7rVbMV/Paths?filterByFormula=AND(LOWER({{Path}}) = '{WebUtility.UrlEncode(path.ToLowerInvariant())}')");


After getting the response, we create an anonymous object (remember those?)
which provides a definition for Json.NET when parsing the JSON response.

    var definition = new { Records = new List<Record>() };
    var result = JsonConvert.DeserializeAnonymousType(response, definition);


We then try to get the data from the first record. If there isn't one, redirect 
will be null and we return a 404  error.

    var redirect = result.Records.FirstOrDefault()?.Fields;

    if(redirect == null)
    {
        return new HttpResponseMessage(HttpStatusCode.NotFound); // Return 404
    }


If we found data we'll create a response message which specifies the browser
should redirect (301). We set the Location  header to be the URL the browser
should redirect to, and return the response message.

    var responseMessage = new HttpResponseMessage(HttpStatusCode.Redirect);
    responseMessage.Headers.Location = new Uri(redirect.Url);

    return responseMessage;
}


See complete code#r "Newtonsoft.Json"

using static System.Environment;
using System.Net.Http;
using System.Net.Http.Headers;
using System.Net;
using Newtonsoft.Json;
using System.Threading.Tasks;

public class Record
{
    public string Id { get; set; }
    public Redirect Fields { get; set; }
    public DateTimeOffset CreatedTime { get; set; }
}

public class Redirect
{
    public string Path { get; set; }
    public string Url { get; set; }
    public string Description { get; set; }
}

public static async Task<HttpResponseMessage> Run(HttpRequestMessage req, string path, TraceWriter log)
{
    var client = new HttpClient
    {
        BaseAddress = new Uri("https://api.airtable.com/")
    };
    client.DefaultRequestHeaders.Authorization = new AuthenticationHeaderValue("Bearer", GetEnvironmentVariable("AirtableApiKey"));

    var response = await client.GetStringAsync($"/v0/appxpeLm4zw7rVbMV/Paths?filterByFormula=AND(LOWER({{Path}}) = '{WebUtility.UrlEncode(path.ToLowerInvariant())}')");

    var definition = new { Records = new List<Record>() };
    var result = JsonConvert.DeserializeAnonymousType(response, definition);

    var redirect = result.Records.FirstOrDefault()?.Fields;

    if(redirect == null)
    {
        return new HttpResponseMessage(HttpStatusCode.NotFound); // Return 404
    }

    var responseMessage = new HttpResponseMessage(HttpStatusCode.Redirect);
    responseMessage.Headers.Location = new Uri(redirect.Url);

    return responseMessage;
}


And that's it!
By adding paths to your Airtable table Paths, you'll add new redirects to your
"redirector". You can do this through the web UI or the excellent apps they have
for iOS and Android.

Happy coding!
