require 'rubygems/package_task'
require 'rake/clean'
require 'rake/extensiontask'

ENV['RAKEHOME'] = File.dirname(File.expand_path(__FILE__))

task 'default' => 'test'

file 'gumbo-parser/src' do
  sh 'git submodule init'
  sh 'git submodule update'
end

file 'lib/nokogumbo.rb' do
  mkdir_p 'lib'
  cp 'nokogumbo.rb', 'lib'
end

EXT = ['ext/nokogumboc/extconf.rb', 'ext/nokogumboc/nokogumbo.c']

task 'setup' => 'gumbo-parser/src'
task 'cross' => 'setup'
task 'compile' => 'setup'

task 'test' => 'compile' do
  ruby 'test-nokogumbo.rb'
end

task 'gem' => 'test'
SPEC = Gem::Specification.new do |gem|
  gem.name = 'nokogumbo'
  gem.version = '0.9'
  gem.email = 'rubys@intertwingly.net'
  gem.homepage = 'https://github.com/rubys/nokogumbo/#readme'
  gem.summary = 'Nokogiri interface to the Gumbo HTML5 parser'
  gem.extensions = 'ext/nokogumboc/extconf.rb'
  gem.author = 'Sam Ruby'
  gem.add_dependency 'nokogiri'
  gem.license = 'Apache 2.0'
  gem.description = %q(
    Nokogumbo allows a Ruby program to invoke the Gumbo HTML5 parser and
    access the result as a Nokogiri parsed document.).strip.gsub(/\s+/, ' ')
  gem.files = EXT + FileList[
    'lib/nokogumbo.rb',
    'LICENSE.txt',
    'README.md',
    'gumbo-parser/src/*'
  ]
end

PKG = Gem::PackageTask.new(SPEC) do |pkg|
  pkg.need_tar = true
  pkg.need_zip = true
end

Rake::ExtensionTask.new('nokogumboc', SPEC) do |ext|
  ext.cross_compile  = true
  ext.cross_platform = ["x86-mingw32"]
end

CLEAN.include FileList.new('ext/nokogumboc/*')-EXT
CLOBBER.include FileList.new('pkg', 'Gemfile.lock')

# silence cleanup operations
Rake::Task[:clobber_package].clear
CLEAN.existing!
CLOBBER.existing!.uniq!
CLOBBER.exclude *Dir['lib/*']
