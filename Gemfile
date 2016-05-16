require 'json'
require 'open-uri'

source 'https://rubygems.org'

versions = JSON.parse(open('https://pages.github.com/versions.json').read)

gem 'github-pages', versions['github-pages']
gem 'html-proofer'
gem 'jekyll'
gem 'jekyll-feed'
