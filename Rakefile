
require 'erb'

projects = {
  'aggregator'      => %w(commons retentions),
  'balancing'       => %w(commons),
  'bifroest'        => %w(commons drains retentions stream-rewriter cluster-client balancing),
  'cluster-client'  => %w(commons retentions balancing),
  'commons'         => %w(),
  'drains'          => %w(commons),
  'heimdall'        => %w(commons cluster-client),
  'retentions'      => %w(commons),
  'stream-rewriter' => %w(drains commons retentions cluster-client),
  'ymir'            => %w(commons retentions)
}
applications = %w(aggregator bifroest heimdall stream-rewriter ymir)

projects.each do |project, _|
  file "repos/#{project}" do
    sh "git clone git@github.com:Bifroest/#{project} repos/#{project}"
  end

  directory "docker_files/#{project}"

  file "docker_files/#{project}/Dockerfile" => "docker_files/#{project}" do
    environment_class= 'TODO'
    b = binding
    File.open("docker_files/#{project}/Dockerfile", 'w+') do |f|
      template_file = File.read 'Dockerfile.erb'
      template = ERB.new template_file
      dockerfile = template.result b
      f.write dockerfile
    end
  end
end

namespace :checkout do
  projects.each do |project, _|
    desc "Checkout #{project} into repo folder"
    task project => "repos/#{project}"
  end

  desc 'Checkout all projects'
  task :all => projects.keys
end

namespace :install do
  projects.each do |project, dependencies|
    desc "Install #{project} into mvn repo"
    task project => [ "checkout:#{project}" ] + dependencies.map { |d| "install:#{d}" } do
      sh "mvn -f repos/#{project} --settings=#{Dir.pwd}/settings.xml install"
    end
  end

  desc "Install all projects"
  task :all => projects.keys
end

namespace :dockerfile do
  projects.each do |project, _|
    task project => [ "docker_files/#{project}/Dockerfile", "install:#{project}" ]
  end

  task :all => projects.keys
end

namespace :dockerimage do
  applications.each do |application|
    task application => "dockerfile:#{application}" do
      sh "sudo docker build -f docker_files/#{application}/Dockerfile -t 'bifroest/#{application}' ."
    end
  end

  task :all => applications
end
