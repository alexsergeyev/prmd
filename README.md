# Prmd

JSON Schema tooling: scaffold, verify, and generate documentation
from JSON Schema documents.


## Introduction

[JSON Schema](http://json-schema.org/) provides a great way to describe
an API. prmd provides tools for bootstrapping a description like this,
verifying its completeness, and generating documentation from the
specification.

To learn more about JSON Schema in general, start with
[this excellent guide](http://spacetelescope.github.io/understanding-json-schema/)
and supplement with the [specification](http://json-schema.org/documentation.html).
The JSON Schema usage conventions expected by prmd specifically are
described in [/docs/schemata.md](/docs/schemata.md).

## Installation

Install the command-line tool with:

```bash
$ gem install prmd
```

If you're using prmd within a Ruby project, you may want to add it
to the application's Gemfile:

```ruby
gem 'prmd'
```

```bash
$ bundle install
```

## Usage

Prmd provides four main commands:

* `init`: Scaffold resource schemata
* `combine`: Combine schemata and metadata into single schema
* `verify`: Verify a schema
* `doc`: Generate documentation from a schema
* `render`: Render views from schema

Here's an example of using these commands in a typical workflow:

```bash
# Fill out the resource schemata
$ mkdir -p schemata
$ prmd init app  > schemata/app.json
$ prmd init user > schemata/user.json
$ vim schemata/{app,user}.json   # edit scaffolded files

# Provide top-level metadata
$ cat <<EOF > meta.json
{
 "description": "Hello world prmd API",
 "id": "hello-prmd",
 "links": [{
   "href": "https://api.hello.com",
   "rel": "self"
 }],
 "title": "Hello Prmd"
}
EOF

# Combine into a single schema
$ prmd combine --meta meta.json schemata/ > schema.json

# Check it’s all good
$ prmd verify schema.json

# Build docs
$ prmd doc schema.json > schema.md
```

# Render from schema

```bash
$ prmd render --template schemata.erb schema.json > schema.md
```

Typically you'll want to prepend header and overview information to
your API documentation. You can do this with the `--prepend` flag:

```bash
$ prmd doc --prepend overview.md schema.json > schema.md
```

You can also chain commands together as needed, e.g.:

```bash
$ prmd combine --meta meta.json schemata/ | prmd verify | prmd doc --prepend overview.md > schema.md
```

See `prmd <command> --help` for additional usage details.

## Documentation rendering settings

Out of the box you can supply a settings file (in either `JSON` or `YAML`) that will tweak the layout of your documentation.

```bash
$ prmd doc --settings config.json schema.json > schema.md
```

Available options (and their defaults)
```json
{
  "doc": {
    "url_style": "default", // can also be "json"
    "disable_title_and_description": false // remove the title and the description, useful when using your own custom header
  }
}
```

## File Layout

We suggest the following file layout for JSON schema related files:

```
/docs (top-level directory for project documentation)
  /schema (API schema documentation)
    /schemata
      /{resource.json} (individual resource schema)
    /meta.json (overall API metadata)
    /overview.md (preamble for generated API docs)
    /schema.json (complete generated JSON schema file)
    /schema.md (complete generated API documentation file)
```

## Contributing

1. Fork it
2. Create your feature branch (`git checkout -b my-new-feature`)
3. Commit your changes (`git commit -am 'Add some feature'`)
4. Push to the branch (`git push origin my-new-feature`)
5. Create new Pull Request
