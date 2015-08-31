#
# Requirements Block
#
require 'rubygems'
require 'rake'
require 'yaml'
require 'jekyll'
require 'html/proofer'
require 'launchy'

#
# Default Task Specification
#
task :default => [:serve]

task :build do
    puts "Compiling the site..."
    Jekyll::Commands::Build.process({
        "source"      => File.expand_path("."),
        "destination" => File.expand_path("./_site")
    })
end

task :preview => [:serve] do
    puts "Opening in browser..."
    Launchy.open("http://127.0.0.1:4000")
end

task :serve => [:build] do
    puts "Running Jekyll..."
    Jekyll::Commands::Serve.process({
        "source"      => File.expand_path("."),
        "destination" => File.expand_path("./_site"),
        "watch"       => true,
        "serve"       => true
        })
end

task :test => [:build] do
    puts "Testing Website..."
    HTML::Proofer.new("./_site", {
        :check_favicon    => true,
        :check_html       => true,
        :href_ignore      => [
            "#",
            ".*127.0.0.1.*",
            "http://127.0.0.1/icingaweb2/setup"
        ],
        :only_4xx         => true,
        :verbose          => true,
        :typhoeus         => {
            :verbose      => false
        },
        :parallel         => {
            :in_processes => 3
        }
    }).run
end
