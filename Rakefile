require 'html/proofer'

task :default => [:test]

task :test do
    sh "bundle exec jekyll build"
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
