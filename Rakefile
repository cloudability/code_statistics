# encoding: utf-8

require 'rubygems'
require 'bundler'
begin
  Bundler.setup(:default, :development)
rescue Bundler::BundlerError => e
  $stderr.puts e.message
  $stderr.puts "Run `bundle install` to install missing gems"
  exit e.status_code
end
require 'rake'

require_relative './lib/code_statistics'

require 'jeweler'
require 'set'
DEVELOPMENT_GROUPS||=[:development, :test, :cli]
RUNTIME_GROUPS||=Set.new(Bundler.definition.groups - DEVELOPMENT_GROUPS)
Jeweler::Tasks.new do |gem|
  gem.name = "code_statistics"
  gem.summary = %Q{Making a gem of the normal rails rake stats method, to make it more robust and work on non rails projects}
  gem.description = %Q{This is a port of the rails 'rake stats' method so it can be made more robust and work for non rails projects. New features may eventually be added as well.}
  gem.license = "MIT"
  gem.email = "engineering@cloudability.com"
  gem.homepage = "http://github.com/cloudability/code_statistics"
  gem.authors = ["Jon Frisby", "Dan Mayer"]
  gem.executables = ['code_statistics']
  gem.required_ruby_version = ">= 1.9.2"
  # gem is a Gem::Specification... see http://www.rubygems.org/read/chapter/20 for additional settings
  # Jeweler wants to manage dependencies for us when there's a Gemfile.
  # We override it so we can skip development dependencies, and so we can
  # do lockdowns on runtime dependencies while letting them float in the
  # Gemfile.
  #
  # This allows us to ensure that using Rocinante as a gem will behave how
  # we want, while letting us handle updating dependencies gracefully.
  #
  # The lockfile is already used for production deployments, but NOT having
  # it be obeyed in the gemspec meant that we needed to add explicit
  # lockdowns in the Gemfile to avoid having weirdness ensue in GUI.
  #
  # This is probably a not particularly great way of handling this, but it
  # should suffice for now.
  gem.dependencies.clear

  Bundler.load.dependencies.select { |dep| (RUNTIME_GROUPS & Set.new(dep.groups)).to_a.count > 0 }.each do |dep|
    dep.requirements_list.each do |req|
      gem.add_dependency(dep.name, req)
    end
  end

  gem.files.reject! do |fn|
    fn =~ /^test\// ||
    fn =~ /^\.document$/ ||
    fn =~ /^\.gitignore$/ ||
    fn =~ /^\.rspec.*$/ ||
    fn =~ /^\.rvmrc$/ ||
    fn =~ /^VERSION$/ ||
    fn =~ /^Gemfile(\.lock)?$/ ||
    fn =~ /^Rakefile$/
  end
end

require 'rake/testtask'
Rake::TestTask.new(:test) do |test|
  test.libs << 'test'
  test.pattern = 'test/**/*_test.rb'
  test.verbose = true
  test.ruby_opts = ["-rtest_helper"]
end

namespace :test do
  desc "Run test suite with coverage analysis."
  task :coverage do
    ENV['USING_COVERAGE']='1'
    Rake::Task[:test].invoke
  end
end


task :default => :test

require 'rdoc/task'
Rake::RDocTask.new do |rdoc|
  if File.exist?('VERSION')
    version = File.read('VERSION')
  else
    version = ""
  end

  rdoc.rdoc_dir = 'rdoc'
  rdoc.title = "code_statistics #{version}"
  rdoc.rdoc_files.include('README*')
  rdoc.rdoc_files.include('LICENSE')
  rdoc.rdoc_files.include('CHANGELOG*')
  rdoc.rdoc_files.include('lib/**/*.rb')
end
