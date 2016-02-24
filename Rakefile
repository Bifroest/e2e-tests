
projects = %w(aggregator retentions bifroest heimdall ymir cluster-client stream-rewriter commons balancing drains)

projects.each do |project|
  file "repos/#{project}" do
    sh "git clone git@github.com:Bifroest/#{project} repos/#{project}"
  end
end

namespace :checkout do
  projects.each do |project|
    desc "Checkout #{project} into repo folder"
    task project => "repos/#{project}"
  end
end

namespace :install do
  projects.each do |project|
    desc "Install #{project} into mvn repo"
    task project => "checkout:#{project}" do
      sh "mvn -f repos/#{project} --settings=#{Dir.pwd}/settings.xml install"
    end
  end

  desc "Install all projects"
  task 'all' => projects
end
