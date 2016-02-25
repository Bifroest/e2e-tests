
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

projects.each do |project, _|
  file "repos/#{project}" do
    sh "git clone git@github.com:Bifroest/#{project} repos/#{project}"
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
