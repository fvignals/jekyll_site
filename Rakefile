require "rubygems"
require 'rake'
require 'yaml'
require 'time'
require "term/ansicolor"
include Term::ANSIColor

SOURCE = "."
CONFIG = {
  'layouts' => File.join(SOURCE, "_layouts"),
  'posts' => File.join(SOURCE, "_posts"),
  'post_ext' => "md"
}


# Usage: rake or rake default
desc "The default task. When run, I will list all available tasks within this document."
task :default do
  system "rake -T"
end # task :default

desc "Generate the static site"
task :build => [:clean, :tags, :categories] do
  system "jekyll"
end # task :build

desc "Launch preview environment"
task :preview => [:clean, :tags, :categories] do
  system "jekyll --auto --server"
end # task :preview

# Usage: rake post title="A Title" [date="2012-02-09"]
desc "Begin a new post in #{CONFIG['posts']}"
task :post do
  abort("rake aborted: '#{CONFIG['posts']}' directory not found.") unless FileTest.directory?(CONFIG['posts'])
  title = ENV["title"] || "new-post"
  slug = title.downcase.strip.gsub(' ', '-').gsub(/[^\w-]/, '')
  begin
    date = (ENV['date'] ? Time.parse(ENV['date']) : Time.now).strftime('%Y-%m-%d')
  rescue Exception => e
    printHeader "Error - date format must be YYYY-MM-DD, please check you typed it correctly!"
    exit -1
  end
  filename = File.join(CONFIG['posts'], "#{date}-#{slug}.#{CONFIG['post_ext']}")
  if File.exist?(filename)
    abort("rake aborted!") if ask("#{filename} already exists. Do you want to overwrite?", ['y', 'n']) == 'n'
  end

  printHeader "Creating new post: #{filename}"
  open(filename, 'w') do |post|
    post.puts "---"
    post.puts "layout: post"
    post.puts "title: \"#{title.gsub(/-/,' ')}\""
    post.puts "categories: "
    post.puts "tags: "
    post.puts "---"

  end
end # task :post

# Usage: rake page name="about.html"
# You can also specify a sub-directory path.
# If you don't specify a file extention we create an index.html at the path specified
desc "Create a new page."
task :page do
  name = ENV["name"] || "new-page.md"
  filename = File.join(SOURCE, "#{name}")
  filename = File.join(filename, "index.html") if File.extname(filename) == ""
  title = File.basename(filename, File.extname(filename)).gsub(/[\W\_]/, " ").gsub(/\b\w/){$&.upcase}
  if File.exist?(filename)
    abort("rake aborted!") if ask("#{filename} already exists. Do you want to overwrite?", ['y', 'n']) == 'n'
  end
  
  mkdir_p File.dirname(filename)
  printHeader "Creating new page: #{filename}"
  open(filename, 'w') do |post|
    post.puts "---"
    post.puts "layout: page"
    post.puts "title: \"#{title}\""
    post.puts "---"
  end
end # task :page

def ask(message, valid_options)
  if valid_options
    answer = get_stdin("#{message} #{valid_options.to_s.gsub(/"/, '').gsub(/, /,'/')} ") while !valid_options.include?(answer)
  else
    answer = get_stdin(message)
  end
  answer
end

def get_stdin(message)
  print message
  STDIN.gets.chomp
end

desc 'Generate tags pages'
task :tags  => :tag_cloud do
  printHeader "Generating tags..."
  require 'rubygems'
  require 'jekyll'
  include Jekyll::Filters
  
  options = Jekyll.configuration({})
  site = Jekyll::Site.new(options)
  site.read_posts('')

  # Remove tags directory before regenerating
  FileUtils.rm_rf("tags")

  site.tags.sort.each do |tag, posts|
    html = <<-HTML
---
layout: default
title: "Tagged: #{tag}"
syntax-highlighting: yes
---
  <h2 class="title">Tag: #{tag}</h2>
  {% for post in site.posts %}
    {% for tag in post.tags %}
      {%if tag == "#{tag}" %}
        {%include post.html%}
      {%endif%}
    {%endfor%}
  {% endfor %}
HTML

    FileUtils.mkdir_p("tags/#{tag}")
    File.open("tags/#{tag}/index.html", 'w+') do |file|
      file.puts html
    end
  end
  printHeader 'Done.'
end

desc 'Generate tag index page'
task :tag_index => :tags do
  printHeader 'Generating tag index page...'
  require 'rubygems'
  require 'jekyll'
  include Jekyll::Filters

  options = Jekyll.configuration({})
  site = Jekyll::Site.new(options)
  site.read_posts('')

  html = ''
  max_count = site.tags.map{|t,p| p.count}.max
  site.tags.sort.each do |tag, posts|
    html << "<a href=\"/tags/#{tag.gsub(/ /,"%20")}\" title=\"Postings in the #{tag} tag\">#{tag}</a> "
  end
  File.open('tags/index.html', 'w+') do |file|
    file.puts html
  end
  printHeader 'Done.'
end

desc 'Generate tag cloud'
task :tag_cloud do
  printHeader 'Generating tag cloud...'
  require 'rubygems'
  require 'jekyll'
  include Jekyll::Filters

  options = Jekyll.configuration({})
  site = Jekyll::Site.new(options)
  site.read_posts('')

  html = ''
  max_count = site.tags.map{|t,p| p.count}.max
  site.tags.sort.each do |tag, posts|
    html << "<a href=\"/tags/#{tag.gsub(/ /,"%20")}\" title=\"Postings tagged #{tag}\">#{tag}</a> "
  end
  File.open('_includes/tag_cloud.html', 'w+') do |file|
    file.puts html
  end
  printHeader 'Done.'
end


desc 'Generate category pages'
task :categories => :category_cloud do
  printHeader "Generating categories..."
  require 'rubygems'
  require 'jekyll'
  include Jekyll::Filters
  
  options = Jekyll.configuration({})
  site = Jekyll::Site.new(options)
  site.read_posts('')

  # Remove tags directory before regenerating
  FileUtils.rm_rf("categories")

  site.categories.sort.each do |category, posts|
    html = <<-HTML
---
layout: default
title: "Posted in category: #{category}"
syntax-highlighting: yes
---
  <h2 class="title">Category: #{category}</h2>
  {% for post in site.posts %}
    {% for category in post.categories %}
      {%if category == "#{category}" %}
        {%include post.html%}
      {%endif%}
    {%endfor%}
  {% endfor %}
HTML

    FileUtils.mkdir_p("category/#{category}")
    File.open("category/#{category}/index.html", 'w+') do |file|
      file.puts html
    end
  end
  printHeader 'Done.'
end

desc 'Generate category index page'
task :category_index => :categories do
  printHeader 'Generating category index page...'
  require 'rubygems'
  require 'jekyll'
  include Jekyll::Filters

  options = Jekyll.configuration({})
  site = Jekyll::Site.new(options)
  site.read_posts('')

  html = ''
  max_count = site.categories.map{|t,p| p.count}.max
  site.categories.sort.each do |category, posts|
    html << "<a href=\"/category/#{category.gsub(/ /,"%20")}\" title=\"Postings in the #{category} category\">#{category}</a> "
  end
  File.open('category/index.html', 'w+') do |file|
    file.puts html
  end
  printHeader 'Done.'
end

desc 'Generate category cloud'
task :category_cloud do
  printHeader 'Generating category cloud...'
  require 'rubygems'
  require 'jekyll'
  include Jekyll::Filters

  options = Jekyll.configuration({})
  site = Jekyll::Site.new(options)
  site.read_posts('')

  html = ''
  max_count = site.categories.map{|t,p| p.count}.max
  site.categories.sort.each do |category, posts|
    html << "<a href=\"/category/#{category.gsub(/ /,"%20")}\" title=\"Postings in #{category}\">#{category}</a> "
  end
  File.open('_includes/category_cloud.html', 'w+') do |file|
    file.puts html
  end
  printHeader 'Done.'
end

desc 'Remove all built files.'
task :clean do
  printHeader "Cleaning build directory..."
  %x[rm -rf _site]
  printHeader "_site directory is now empty. Housecleaning rocks!"
end

def printHeader headerText
  print bold + blue + "==> " + reset
  print bold + headerText + reset + "\n"
end