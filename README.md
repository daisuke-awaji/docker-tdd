# DockerコンテナをTDDで開発していくための最小限のTips

```
$ tree
.
├── Dockerfile
├── Gemfile
├── Gemfile.lock
├── README.md
└── spec
    ├── spec_helper.rb
    └── web
        └── git_spec.rb
```

spec_helper.rb

```ruby
require 'serverspec'
require 'docker'

set :backend, :docker
set :docker_image, 'web'
```

```bash
$ bundle install
```

spec_helperにset :backend, :dockerとset :docker_image, 'image_name'を指定すると、 serverspecがテストを実行するときに自動でdocker imageに対してdocker execコマンドを実行し、テストしてくれるようになります。


```bash
$ docker build -t web .
Sending build context to Docker daemon   59.9kB
Step 1/1 : FROM centos
 ---> 49f7960eb7e4
Successfully built 49f7960eb7e4
Successfully tagged web:latest

$ docker images
REPOSITORY                                               TAG                 IMAGE ID            CREATED             SIZE
web                                                      latest              49f7960eb7e4        5 weeks ago         200MB
centos                                                   latest              49f7960eb7e4        5 weeks ago         200MB
```

# テストを失敗させる RED


```ruby
$ bundle exec rspec

Package "git"
  should be installed (FAILED - 1)

Failures:

  1) Package "git" should be installed
     Failure/Error: it { should be_installed }
       expected Package "git" to be installed

     # ./spec/web/git_spec.rb:4:in `block (2 levels) in <top (required)>'

Finished in 1.09 seconds (files took 0.42839 seconds to load)
1 example, 1 failure

Failed examples:

rspec ./spec/web/git_spec.rb:4 # Package "git" should be installed
```

# Dockerfileを書いていく

```bash
$ emacs Dockerfile
```


```
FROM centos
RUN yum install -y git
```

# テストを成功させる GREEN

```ruby
$ rspec

Package "git"
  should be installed

Finished in 1.13 seconds (files took 0.89826 seconds to load)
1 example, 0 failures
```
