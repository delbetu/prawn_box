# Getting started

To run the application locally, you should:

* `bundle install` to install the Ruby dependencies.
* `bundle exec rake` to build the `.wasm` application file.
* `bundle exec rake build_site` to build the site inti `./dist`
* `ruby -run -e httpd './dist' -p 8000` serve the site from dist.
* Open a browser at `localhost:8000`.
