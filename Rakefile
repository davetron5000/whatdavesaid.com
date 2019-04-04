require 'webrick'
require 'pathname'
include FileUtils

task :default => [ :build ]
task :build do
  cp "src/html/index.html", "site/index.html"
  cp "src/html/404.html", "site/404.html"
end

desc "Serve up site locally"
task :serve => :build do
  WEBrick::HTTPServer.new(:Port => 8000, :DocumentRoot => Pathname(Dir.pwd) / "site").start
end

desc "Deploy to S3"
task :deploy => :build do
  fail "Must be run from root" unless Dir.exist?("site")
  [
    "--cache-control=\"max-age=3600\"",
  ].each do |args|
    command = "aws s3 sync #{args} --profile=personal site/ s3://whatdavesaid.com"
    puts command
    sh(command) do |ok,res|
      fail res.inspect unless ok
    end
  end
  sh "aws cloudfront create-invalidation --profile=personal --distribution-id=E8T2XNPHWMAQO --paths=/index.html"
end
