# frozen_string_literal: true

file "wasi-vfs" do
  version = "0.2.0"
  filename =
    if ENV["CI"]
      "wasi-vfs-cli-x86_64-unknown-linux-gnu.zip"
    else
      "wasi-vfs-cli-x86_64-apple-darwin.zip"
    end

  `curl -LO "https://github.com/kateinoigakukun/wasi-vfs/releases/download/v#{version}/#{filename}"`
  `unzip #{filename}`
  rm filename
end

file "head-wasm32-unknown-wasi-full-js" do
  require "json"
  version = JSON.parse(File.read("package.json"))["dependencies"]["ruby-head-wasm-wasi"][1..]
  filename = "ruby-head-wasm32-unknown-wasi-full-js.tar.gz"

  `curl -LO https://github.com/ruby/ruby.wasm/releases/download/ruby-head-wasm-wasi-#{version}/#{filename}`
  `tar xfz #{filename}`
  rm filename
end

file "ruby.wasm" => ["head-wasm32-unknown-wasi-full-js"] do
  cp "head-wasm32-unknown-wasi-full-js/usr/local/bin/ruby", "ruby.wasm"
end

file "app.wasm" => ["Gemfile.lock", "wasi-vfs", "ruby.wasm"] do
  require "bundler/setup"

  # copy gems from bundler directories into "./lib" directory
  prawn_lib_dir =  $:.find { _1.match?(/prawn-\d\.\d\.\d/)}
  cp_r prawn_lib_dir, "."

  prawn_table_lib_dir =  $:.find { _1.match?(/prawn-table-\d\.\d\.\d/)}
  cp_r prawn_table_lib_dir, "."

  # copy assets
  prawn_fonts_files = prawn_lib_dir + "/../data/fonts/"
  cp_r prawn_fonts_files, "./head-wasm32-unknown-wasi-full-js/usr/local/lib/afm"

  # copy dependencies
  cp_r $:.find { _1.include?("ttfunk") }, "."
  cp_r $:.find { _1.include?("pdf-core") }, "."

  `./wasi-vfs pack ruby.wasm --mapdir /lib::./lib --mapdir /usr::./head-wasm32-unknown-wasi-full-js/usr -o app.wasm`
  rm_rf "lib"
end

task default: ["app.wasm"]
