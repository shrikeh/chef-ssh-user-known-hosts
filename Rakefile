#!/usr/bin/env rake
require 'rspec/core/rake_task'
require 'rubocop/rake_task'
require 'foodcritic'

# Style tests. Rubocop and Foodcritic
namespace :style do
  desc 'Run Ruby style checks'
  RuboCop::RakeTask.new(:ruby)

  desc 'Run Chef style checks'
  FoodCritic::Rake::LintTask.new(:chef) do |t|
    t.options = {
      context: true,
      fail_tags: ['~FC015'],
      exclude_paths: ['spec/', 'test/']
    }
  end
end

desc 'Run all style checks'
task style: %w( style:chef style:ruby )

# Rspec and ChefSpec
desc 'Run ChefSpec examples'
RSpec::Core::RakeTask.new(:spec)

# Integration tests. Kitchen.ci
namespace :integration do
  desc 'Run Test Kitchen with Vagrant/Docker'
  task :kitchen do
    require 'kitchen'

    Kitchen.logger = Kitchen.default_file_logger
    loader = ::Kitchen::Loader::YAML.new(
      project_config: ENV['KITCHEN_YAML'],
      local_config: ENV['KITCHEN_LOCAL_YAML'],
      global_config: ENV['KITCHEN_GLOBAL_YAML']
    )
    config = ::Kitchen::Config.new(
      loader: loader
    )
    config.instances.each do |instance|
      instance.test(:always)
    end
  end
end

namespace :test do
  task quick: %w( style spec )
  task full: %w( style spec integration:kitchen )
end

task default: 'test:quick'
