# Sinatra React Lab: Chatterbox

## Learning Goals

- Create an API with Sinatra for a React frontend application

## Introduction

So far, we've seen how to build a Sinatra API and perform various CRUD actions
using Active Record. In this lab, you'll work on creating an API in Sinatra once
more — but this time, you'll also have code for a React frontend application, so
you can get a taste of full-stack development!

This project is separated into two applications:

- A React frontend, in the `chatterbox-client` directory
- A Sinatra backend, in the `chatterbox-server` directory

All of the features for the React frontend are built out, and we have a simple
`json-server` API that you can run to see what the completed version of the app
will look like. Your main goal with this lab is to build out a Sinatra API
server to replace `json-server`, so most of your coding will be done in the
backend.

## Frontend Setup

Let's take a quick tour of what we have so far.

To get started, `cd` into the `chatterbox-client` directory. Then run:

```console
$ npm install
$ npm run server
```

This will install the React project dependencies, and run a demo API server
using `json-server`. Next, run this in a new terminal:

```console
$ npm start
```

Then visit [http://localhost:3000](http://localhost:3000) in the browser and
interact with the demo application to get a sense of its features.

Here's a demo of the what the React app should look like when using
`json-server` as the API:

![Chatterbox demo](https://curriculum-content.s3.amazonaws.com/phase-3/chatterbox-sinatra-react-lab/chatterbox-demo.gif)

Take a look at the components provided in the `chatterbox-client` directory.
Explore the code and pay special attention to where the React application is
interacting with `json-server`. Where are the `fetch` requests being written?
What routes are needed to handle these requests? What HTTP verbs? What data is
being sent in the body of the requests?

Once you've familiarized yourself yourself with the code, turn off `json-server`
with `control + c` in the terminal where `json-server` is running (you can keep
the React application running, though). Next, let's see what we have in the
backend.

## Backend Setup

In another terminal, `cd` into the `chatterbox-server` directory, and run
`bundle install` to install the dependencies.

In this directory, you're given a bare-bones template for a Sinatra API
application. It should look familiar to other Sinatra labs you've seen and has
all the code set up so you can focus on building out your model and API routes.

You'll be responsible for:

- Creating a model and migrations
- Setting up the necessary routes to handle requests
- Performing CRUD actions with Active Records
- Sending the necessary JSON data in the responses

### Allowing Frontend Requests: CORS

The only new code for the server is the [Rack CORS gem][rack-cors]. This gem
provides some Rack middleware which we need to configure so that applications
running in the browser, like our React client, can make requests to the backend.

If we didn't use this gem, any requests from our React frontend in the browser
would result in an error message like this:

```txt
Access to fetch at 'http://localhost:9292/messages' from origin
'http://localhost:3000' has been blocked by CORS policy: No
'Access-Control-Allow-Origin' header is present on the requested resource. If an
opaque response serves your needs, set the request's mode to 'no-cors' to fetch
the resource with CORS disabled.
```

The reason for this warning message is due to a browser security feature known as
[Cross-Origin Resource Sharing (CORS)][cors mdn]. When we use JavaScript from
one domain (aka origin) to make a request to a server on a different domain, the
default behavior of the browser is to block those requests from going through.

For example, if I own the website `definitelynotahacker.com`, I can't use
JavaScript to make a network request to `api.yourbankaccount.com`, unless
`api.yourbankaccount.com` explicitly gives permission to my website.

To give that permission, any server that we want to make requests to using
JavaScript must add some special **headers** to the response that tell the
browser that the request was permitted.

Here's what the CORS configuration looks like (in the `config.ru` file):

```rb
# Allow CORS (Cross-Origin Resource Sharing) requests
use Rack::Cors do
  allow do
    # origins '*' allows requests from ALL frontend origins
    # (if you deploy your application, change this to
    # ONLY allow requests from YOUR frontend origin, like:
    # origins 'my-react-app.netlify.app')
    origins '*'
    resource '*', headers: :any, methods: [:get, :post, :delete, :put, :patch, :options, :head]
  end
end
```

You don't have to make any changes to this configuration to complete this lab,
but CORS warnings are a very common thing to encounter in web development, so
next time you see them, you'll know what this means!

## Instructions

Work through the deliverables below. There are tests in the `chatterbox-server`
folder. Because of this lab's project structure, running `learn test` won't work
— instead, you'll need to `cd` into the `chatterbox-server` directory and run
`rspec` (or `bundle exec rspec`) to run the tests for the Sinatra backend.

Make sure to try out your routes from the React frontend application as well
once you have everything set up. You can run your Sinatra server with:

```console
$ bundle exec rake server
```

You'll need to change the `fetch` requests in React to use
`http://localhost:9292` as well.

### Model

Start by generating a `Message` model and the necessary migration code to create
messages with the following attributes:

- body: string
- username: string
- created_at: timestamp
- updated_at: timestamp

After creating the model and migrations, run the migrations and use the provided
`seeds.rb` file to seed the database:

```console
$ bundle exec rake db:migrate db:seed
```

### Routes

Build out the following routes to handle the necessary CRUD actions:

- `GET /messages`: returns an array of all messages as JSON, ordered by
  `created_at` in ascending order.
- `POST /messages`: creates a new message with a `body` and `username` from
  params, and returns the newly created post as JSON.
- `PATCH /messages/:id`: updates the `body` of the message using params, and
  returns the updated message as JSON.
- `DELETE /messages/:id`: deletes the message from the database.

## Resources

- [CORS Explained][cors mdn]
- [The rack-cors Gem][rack-cors]

[cors mdn]: https://developer.mozilla.org/en-US/docs/Web/HTTP/CORS
[rack-cors]: https://github.com/cyu/rack-cors
