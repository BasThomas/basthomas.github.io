source 'https://rubygems.org'

gem 'jekyll'

group :jekyll_plugins do
   gem 'jekyll-feed'
end

require 'json'
require 'open-uri'
versions = JSON.parse(open('https://pages.github.com/versions.json').read)

gem 'github-pages', versions['github-pages']
