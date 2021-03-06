# encoding: UTF-8

# Copyright 2012 Twitter, Inc
# http://www.apache.org/licenses/LICENSE-2.0

require 'rubygems' unless ENV['NO_RUBYGEMS']

require 'bundler'
require 'digest'

require 'rspec/core/rake_task'
require 'rubygems/package_task'

require './lib/twitter_cldr'

Bundler::GemHelper.install_tasks

task :default => :spec

desc 'Run specs'
RSpec::Core::RakeTask.new do |t|
  t.pattern = './spec/**/*_spec.rb'
end

namespace :spec do
  desc 'Run full specs suit'
  task :full => [:full_spec_env, :spec]

  task :full_spec_env do
    ENV['FULL_SPEC'] = 'true'
  end
end

if RUBY_VERSION < '1.9.0'
  desc 'Run all examples with RCov'
  RSpec::Core::RakeTask.new('spec:rcov') do |t|
    t.rcov      = true
    t.pattern   = './spec/**/*_spec.rb'
    t.rcov_opts = %w(-T --sort coverage --exclude gems/,spec/)
  end
end

namespace :update do
  desc 'Import locales resources'
  task :locales_resources, :cldr_path do |_, args|
    TwitterCldr::Resources::LocalesResourcesImporter.new(
        args[:cldr_path] || './vendor/cldr',
        './resources/locales'
    ).import
  end

  desc 'Import custom locales resources'
  task :custom_locales_resources do
    TwitterCldr::Resources::CustomLocalesResourcesImporter.new('./resources/custom/locales').import
  end

  desc 'Import tailoring resources from CLDR data (should be executed using JRuby 1.7 in 1.9 mode)'
  task :tailoring_data, :cldr_path, :icu4j_jar_path do |_, args|
    TwitterCldr::Resources::TailoringImporter.new(
        args[:cldr_path] || './vendor/cldr',
        './resources/collation/tailoring',
        args[:icu4j_jar_path] ||'./vendor/icu4j-49_1.jar'
    ).import(TwitterCldr.supported_locales)
  end

  desc 'Import Unicode data resources'
  task :unicode_data, :unicode_data_path do |_, args|
    TwitterCldr::Resources::UnicodeDataImporter.new(
        args[:unicode_data_path] || './vendor/unicode-data',
        './resources/unicode_data'
    ).import
  end

  desc 'Import composition exclusions resource'
  task :composition_exclusions, :derived_normalization_props_path do |_, args|
    TwitterCldr::Resources::CompositionExclusionsImporter.new(
        args[:derived_normalization_props_path] || './vendor/unicode-data/DerivedNormalizationProps.txt',
        './resources/unicode_data'
    ).import
  end

  desc 'Import postal codes resource'
  task :postal_codes, :cldr_path do |_, args|
    TwitterCldr::Resources::PostalCodesImporter.new(
        args[:cldr_path] || './vendor/cldr',
        './resources/shared'
    ).import
  end

  desc 'Import phone codes resource'
  task :phone_codes, :cldr_path do |_, args|
    TwitterCldr::Resources::PhoneCodesImporter.new(
        args[:cldr_path] || './vendor/cldr',
        './resources/shared'
    ).import
  end

  desc 'Import language codes'
  task :language_codes, :language_codes_data do |_, args|
    TwitterCldr::Resources::LanguageCodesImporter.new(
        args[:language_codes_data] || './vendor/language-codes',
        './resources/shared'
    ).import
  end

  desc 'Update default and tailoring tries dumps'
  task :collation_tries do
    TwitterCldr::Resources::CollationTriesDumper.update_dumps
  end

  desc 'Update canonical compositions resource'
  task :canonical_compositions do
    TwitterCldr::Resources::CanonicalCompositionsUpdater.new('./resources/unicode_data').update
  end
end
