require 'fileutils'

task :default do
  puts "Usage:"
  puts "  rake \"My New Post\"          # creates a post in _posts"
  puts "  rake draft \"My New Draft\"   # creates a draft in _drafts"
  exit 1
end
# Register all ARGV entries as dummy tasks to prevent Rake errors
ARGV.each { |a| task a.to_sym do; end }

if ARGV[0] == 'draft'
  # Draft mode: rake draft "My Title"
  title = ARGV[1]

  if title.nil? || title.strip.empty?
    puts "Usage: rake draft \"My Draft Title\""
    exit 1
  end

  slug = title.downcase.strip.gsub(' ', '-').gsub(/[^\w-]/, '')
  filename = "#{slug}.md"
  path = File.join('_drafts', filename)

  FileUtils.mkdir_p('_drafts')

  content = <<~STR
    ---
    layout: post
    title: #{title}
    ---
  STR

  File.write(path, content)
  puts "Created draft: #{path}"
  exit 0

elsif ARGV[0] == 'publish'
  title = ARGV[1]

  if title.nil? || title.strip.empty?
    puts "Usage: rake publish \"My Draft Title\""
    exit 1
  end

  drafts = Dir.glob('_drafts/*.md').sort
  slug = title.downcase.strip.gsub(' ', '-').gsub(/[^\w-]/, '')
  draft_path = File.join('_drafts', "#{slug}.md")

  unless File.exist?(draft_path)
    # Check if title is a number — pick from sorted list
    if title =~ /^\d+$/
      index = title.to_i - 1
      if index < 0 || index >= drafts.length
        puts "Invalid number. Only #{drafts.length} draft(s) available."
        exit 1
      end
      draft_path = drafts[index]
    else
      # Fuzzy match — find closest names
      matches = drafts.select { |d| d.include?(slug) || slug.chars.each_cons(3).any? { |c| d.include?(c.join) } }

      if matches.empty?
        puts "No drafts found matching \"#{title}\". All drafts:"
        drafts.each_with_index { |d, i| puts "  #{i+1}. #{File.basename(d)}" }
      else
        puts "No exact match found. Did you mean:"
        matches.each_with_index { |d, i| puts "  #{i+1}. #{File.basename(d)}" }
      end
      exit 1
    end
  end

  date = Time.now.strftime('%Y-%m-%d')
  slug = File.basename(draft_path, '.md')
  filename = "#{date}-#{slug}.md"
  post_path = File.join('_posts', filename)

  content = File.read(draft_path)
  content = content.sub(/\A---\n/, "---\ndate: #{date}\n")

  FileUtils.mkdir_p('_posts')
  File.write(post_path, content)
  File.delete(draft_path)

  puts "Published: #{draft_path} → #{post_path}"
  exit 0

elsif ARGV[0] && ARGV[0] != 'default'
  # Post mode: rake "My Title"
  title = ARGV[0]

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
  puts "Created post: #{path}"
  exit 0
end
