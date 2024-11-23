# Prawnbox

Fast preview your prawn code.
Type the ruby code in the left side, click preview check the output pdf on the right side.

If you're a ruby developer you probably had to write code that produces a pdf using prawn.  
The process for writing previewing usually involve several steps such us save the file, go to browser generate the pdf, find what is wrong, fix and repeat.

This tool aims to streamline that process by providing a split view code | pdf-preview.

It uses WebAssembly under the hood so it can interpret your ruby code.

# Website

https://delbetu.github.io/prawn_box/

# Getting started

To run the application locally, you should:

* `bundle install` to install the Ruby dependencies.
* `bundle exec rake` to build the `.wasm` application file.
* `bundle exec rake build_site` to build the site inti `./dist`
* `ruby -run -e httpd './dist' -p 8000` serve the site from dist.
* Open a browser at `localhost:8000`.


# Contributing

The system is composed of:

app.wasm    ==> ruby interpreter + gems installed into a virtual file system and compiled to wasm
Gemfile     ==> Hold dependencies (prawn, prawn-table) to be included in app.wasm
Rakefile    ==> app.wasm task holds the building process for app.wasm
index.html  ==> UI (creates a webassembly module for app.wasm using ruby-wasm-wasi npm package from ruby.wasm)

## How pdf generation works
The main view needs to eval ruby code using the prawn library.

```ruby
require "rubygems"
$:.unshift("/lib") # virtual file system has this folder with prawn and prawn-table
require "prawn"
require "prawn/table"

pdf = Prawn::Document.new
pdf.instance_eval do
  text "Hello World!"
end
pdf.render
```

For that, it reads the html input (user ruby code) and calls RubyVm.eval(...).
The RubyVm actually is a prepared environment containing.
The interpreter `ruby.wasm` and other gems (`prawn` and `prawn-table`).
This ruby.wasm executes the ruby code and returns a binary string (the pdf)
index.html receives that string and puts it that into the `<embed>` tag.
