# Prawnbox

✨ **Fast preview your Prawn code!** ✨  
📄 Type the Ruby code on the **left side**  
➡️ click **Preview**  
👀 check the output PDF on the **right side**.   

If you're a Ruby developer, you’ve probably had to write code that generates PDFs using [Prawn](https://github.com/prawnpdf/prawn).  
The usual process for previewing involves several tedious steps:  
1 Save the file.  
2 Open your browser.  
3 Generate the PDF.  
4 Find what’s wrong.  
5 Fix and repeat. 🔄  

⚡ **This tool makes your life easier** by offering a split view: **Code | PDF Preview**. 

It leverages the power of **WebAssembly** under the hood, so it can directly interpret your Ruby code in the browser! 🚀

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
