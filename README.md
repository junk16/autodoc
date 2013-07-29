# Autodoc
Auto-generate JSON API documents from your request-specs.

## Installation
```ruby
gem "autodoc", group: :test
```

## Usage
All request-specs tagged with `:autodoc` will be auto-documented.  
You must include a HTTP method and request path in the example description.

```
$ AUTODOC=1 rspec
```

```ruby
# spec/requests/recipes_spec.rb
describe "Recipes" do
  let(:params) do
    { name: "alice", type: 1 }
  end

  describe "POST /recipes", autodoc: true do
    it "creates a new recipe" do
      post "/recipes.json", params
      response.status.should == 201
    end
  end
end
```

and the following document is generated in [doc/recipes.md](https://github.com/r7kamura/autodoc/blob/master/spec/dummy/doc/recipes.md).

<pre>
## POST /recipes
Creates a new recipe

```
POST /recipes
```

### parameters
* `name` string (required)
* `type` integer

### response
```
Status: 201
location: http://www.example.com/recipes/1
response: 
{
  "created_at" => "2013-06-07T08:28:35Z",
          "id" => 1,
        "name" => "alice",
        "type" => 1,
  "updated_at" => "2013-06-07T08:28:35Z"
}
```
</pre>

## Custom template
You can customize autodoc's template ([Example](https://github.com/r7kamura/autodoc/blob/master/lib/autodoc/document.rb#L126-L139)).
Here are avilable variables:

* description
* method
* parameters_section
* path
* request_body_section
* response_body
* response_headers
* response_status

<pre>
Autodoc.configuration.template = &lt;&lt;-EOF
  ## &lt;%= request.method %&gt; &lt;%= path %&gt;
  &lt;%= example.description.capitalize %&gt;.
  &lt;%= parameters_section %&gt;
  ### request
  ```
  &lt;%= request.method %&gt; &lt;%= request.path %&gt;
  ```
  &lt;%= request_body_section %&gt;
  ### response
  ```ruby
  Status: &lt;%= response.status %&gt;&lt;%= headers %&gt;
  response: &lt;%= response_body %&gt;
  ```

EOF
</pre>
