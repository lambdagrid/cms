# What is LambdaGrid CMS?

LambdaGrid CMS is an API web service for developers to no longer be responsible for updating text or correcting typos inside of a web app or a marketing site. This includes headers and paragraphs, buttons and calls to action, and help and explanation text.

# Why do developers need LambdaGrid CMS?

Currently, most developers hardcode all their app's copywriting directly into the source code. Maybe it's pulled from a JSON file, but likely it's embedded directly into the HTML.

Any time somebody wants to update the copy, whether a marketer or product manager or exec, a developer needs to update the source and trigger a deploy. Developers tend to be extremely unenthused about updating copy. The Text API allows developers to integrate one API and then never have to update copy themselves again.

Developers can insert text while building their app or site with the CMS. Any time a product manager or marketer wants to update copywriting, they can update via the web console or the API for CMS. They can update copywriting without any developer intervention.

Developers can focus on shipping code, not copywriting, with the CMS.

# Getting started with LambdaGrid CMS in your application

The quick and dirty integration is getting all copywriting key/val pairs at your app's startup time and storing them as constants.

Here's an example integration in a React app:

```javascript
const HelloWorld = () => {
  return (<div>
    <h1>{{window.copy['homepage-header']}}</h1>
    <h2>{{window.copy['homepage-subheader']}}</h2>
    <p>{{window.copy['homepage-paragraph']}}</p>
    <a class="btn btn-primary" href="/sign-up">{{window.copy['homepage-CTA-button']}}</a>
  </div>);
};

async function init(){
  const encodedCredentials = base64.encode(`${user}:${password}`);
  const headers = new Headers({ 'Authorization': `Basic: ${encodedCredentials}`);
  const response = await fetch('https://cms-api.your-company.com/copy', {headers});
  window.copy = await response.json();
  ReactDOM.render(<HelloWorld />, document.getElementById('root'));
}

init();
```

A quick and dirty Ruby on Rails integration might look like the following:

```ruby
config.after_initialize do
  url = 'https://text-api.your-company.com/copy'
  credentials = [user, password]
  config.copy = open(url, http_basic_authorization: credentials).read
end
```

# How can I use the CMS?

The LambdaGrid CMS API server is free open source software. You can fork the repository and spin up your own CMS server, or you can pay for a managed CMS server with a web console for non-technical users who want a graphical user interface instead of an API.

Assuming you host your CMS server at `cms-api.your-company.com`, you can interact with the API as follows:

```bash
##########
# First, let's get all copy as key/val pairs. We'll pretty print them with python.

$ curl https://cms-api.your-company.com/copy \
    -u user-with-read-access:password \
  | python -m json.tool
{
    "homepage-header": "Welcome to Your Company!",
    "homepage-CTA-button": "Start with Your Company today"
}

##########
# Now, let's upsert some new copy.

# We'll declare some new key/val pairs that we want for our homepage.
$ cat upsert-new-copy.json
{
     "homepage-CTA-button": "Sign up now",
     "homepage-subheader": "Your friendly neighborhood company"
}

# Let's send these key/val pairs to the Text API.
$ curl -XPOST https://cms-api.your-company.com/copy \
    -u user-with-write-access:password \
    -H 'content-type: application/json' \
    -d @upsert-new-copy.json

# Now let's verify that the new key/val pairs were added.
$ curl https://cms-api.your-company.com/copy \
    -u user-with-read-access:password \
  | python -m json.tool
{
    "homepage-header": "Welcome to Your Company!",
    "homepage-CTA-button": "Sign up now",
    "homepage-subheader": "Your friendly neighborhood company"
}

# Notice how "home-CTA-button" changed from "Start with Your Company today"
# to "Sign up now". Notice also that the third key/val pair "homepage-
# subheader" is now also accessible to any apps consuming the Text API.

##########
# Finally, let's delete some key/val pairs.

# We'll send the delete request for a specific key/val pair.
$ curl -XDELETE https://cms-api.your-company.com/copy/homepage-subheader \
    -u user-with-write-access:password

# Now let's verify that the new key/val pair was deleted.
$ curl https://cms-api.your-company.com/copy \
    -u user-with-read-access:password \
  | python -m json.tool
{
    "homepage-header": "Welcome to Your Company!",
    "homepage-CTA-button": "Sign up now"
}

# Notice how "homepage-subheader" is absent.
```
