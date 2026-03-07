require 'fileutils'

task :default do
  puts "Usage: rake \"My New Post Title\""
  exit 1
end

# Catch-all: intercept any unknown task name as the post title
task :method_missing

Rake.application.instance_eval do
  def [](*args)
    task = super rescue nil
    task
  end
end

ARGV.each { |a| task a.to_sym do; end }

title = ARGV[0]

if title && title != 'default'
  date = Time.now.strftime('%Y-%m-%d')
  slug = title.downcase.strip.gsub(' ', '-').gsub(/[^\w-]/, '')
  filename = "#{date}-#{slug}.md"
  path = File.join('_posts', filename)

  FileUtils.mkdir_p('_posts')

  content = <<~STR
    ---
    layout: post
    title: #{title}
    date: #{date}
    ---
  STR

  File.write(path, content)
  puts "Created: #{path}"
  exit 0
end