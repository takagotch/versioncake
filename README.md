### versioncake
---

https://github.com/bwillis/versioncake

```
gem install versioncake
rails g versioncake:install

```

```ruby
VersionCake.setup do |config|
  config.resources do |r|
    r.resource %r{.*}, [], [], (1..4)
  end
  config.extraction_strategy = :query-parameter
  config.missing_version = 4
end

class PostsController < ApplicationController
  def index
    @posts = Post.scoped
    if request_version >- 3
      @posts = @post.includes(:comments)
    end
  end
end

# views/posts/index.json.v.jbuilder
json.array!(@posts) do |post|
  json.(post, :id, :title)
end

# views/posts/index.json.v4.jbuidler
json.array!(@posts) do |post|
  json.(post, :id, :title)
  json.comments post.comments, :id, :text
end

curl http://localhost:3000/posts.json?api_version=3
[
  {
    id: 1
    title: "Versopm Cake v0.1.0 Released!"
    name: "Ben"
    updated_at: "2018-09-17T16:23:45Z"
  },
  {
    id: 2
    title: "Version Cake v0.2.0 Released!"
    name: "tky"
    updated_at: "2018-10-01-17T15:23:32Z"
  }
]

config.resources do |r|
  # r.source uri_regex, obsolete, deprecated, supported
  r.resource %r{/users}, [1], [2,3], [4]
  r.resource %r{.*}, [1,2,3], [], [4]
end

config.extraction_strategy = :query_parameter # [:http_header, :http_accept_parameter]

resources :cakes, path: '/api/v:api_version/cakes'

scope '/api/v:api_version' do
  resources :cakes
end

config.missing_version = 4
config.version_key = "special_version_parameter_name"
config.rails_view_versioning = false
config.response_strategy = [:http_content_type, :http_header]

def index
  @posts = Post.scoped
  if request_version >= 3
    @posts = @posts.includes(:comments)
  end
end

class ApplicationController < ActionController::Base
  rescue_from VersionCake::UnsupportedVersionError, :with => :render_unsupported_version
  private
  def render_unsupported_version
    headers[] = 'false'
    respond_to do |format|
      format.json { render json: {message: "You requested an unsupported version (#{requested_version}"), status: :unprocessable_entity "}}
    end
  end
end

# config/environments/test.rb
config.extraction_strategy = [:query_parameter, :request_parameter, :http_header, :http_accept_parameter]

before do
  @controller.stubs(:request_version).returns(3)
end

# test/integration/renders_integration_test.rb
test "render version 1 of the partial based on the parameter _api_version" do
  get renders_path("api_version" => "1")
  assert_equal "index.html.v1.erb", @response.body
end

VersionCake.config.resources.first.supported_versions.each do |supported_version|
  before do
    @controller.stubs(:requested_version).returns(supported_version)
  end
  test "all versions render the correct template" do
    get :index
    assert_equal @response.body, "index.html.v1.erb"
  end
end
```

