## [Create user endpoints](https://www.pivotaltracker.com/story/show/117090017)

#### Jen Diamond

+ GET `api/v1/users`      (index)
+ POST `api/v1/users`     (create)
+ PUT `api/v1/users`      (update)
+ GET `api/v1/users/:id`  (show)

**Do not forget tests :)**

===

### Steps:

#### Update routes

**`config/routes.rb`**
```
Rails.application.routes.draw do
  get 'api/v1/ping' => 'main#ping'
  resources :users, only: [:index, :create, :update, :show], constraints:
{format: :json}
end
```

#### Add User Serializer

**`app/serializers/user_serializer.rb`**
```
class UserSerializer < ActiveModel::Serializer
  attributes :id, :email, :created_at, :updated_at
end
```

#### Add actions to the `users_controller`

**`app/controllers/api/v1/users_controller.rb`**
```
class UsersController < ApplicationController
  before_action :find_user, only: [:show, :update]

  def index
    users = User.all
  end

  def show
    render json: user
  end

  def create
    user = User.new(user_params)
    if user.save
      render json: user, status: 201
    else
      render json: { errors: user.errors }, status: 422
    end
  end

  def update
    if user.update(user_params)
      render json: user, status: 200
    else
      render json: { errors: user.errors }, status: 422
    end
  end

  private

  def find_user
    user = User.find(params[:id])
  end

  def user_params
    params.permit(:email,:id)
  end

end
```

