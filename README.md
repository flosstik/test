## 1. Rails controller refactoring
#### Objectifs
Rewrite the following Rails Controller for:
* A better control flow and naming
* Improve its security

#### Hint
`mobile_app` attributes are `id: Integer`, `name: String`, `store_ids: Array`

### before:

```ruby
class MobileAppsController < ApplicationController
  before_action :require_authenticated_user
  before_action :find_store
  before_action :find_mobile_app, only: [:show]
  before_action :check_stats_permissions

  def index
    query = MobileAppsService.new(@store)
    respond_to do |format|
      format.html { @mobile_apps = query.all }
    end
  end

  def show
    render 'show'
  end

  def create
    @app = MobileAppsService.new(@store).new_app(params[:mobile_app])
    if @app.save
      render 'create'
    else
      render_errors @app
    end
  end

  def update
    @app = MobileApp.find(params[:id])
    if @app.update_attributes(params[:mobile_app])
      render 'create'
    else
      render_errors @app
    end
  end

  private

  def find_store
    @store = Store.find_by(id: params[:store_id])
    render_access_forbidden unless @store
  end

  def check_stats_permissions
    render_access_forbidden unless current_user.has_permissions?(@store)
  end

  def find_mobile_app
    @app = MobileApp.find_by(id: params[:id])
    render_not_found unless @app
  end
end
```


## 2. Write a program
1. Objective
2. Technologies used
3. Specificities
4. Next steps

## Objective :
The main goal is to create a service that would allow us to upload a questionnaire and retrieve its data in JSON format.
Form data would need to be validated before being saved

Description :

* Simple page allowing to upload a yaml file. POST `localhost:3000/questionnaires`  
  (upload file is yaml because it is less verbose than json)
* Validation on questionnaire format
* Endpoint to retrieve questionnaire data from its reference. GET `localhost:3000/questionnaires/:reference`
* Write at least one test on implemented features
* Questionnaire example is the [following](./questionnaire.yml)

## Technologies :
Ruby on Rails, database choice is free
Rspec for unit tests


## Specificities :
### Upload
* Upload page, GET `localhost:3000/questionnaires/new` allow to choose which file to upload
* POST `localhost:3000/questionnaires` with chosen file
* If questionnaire is valid, redirect to uplaod page with message `L'upload du questionnaire a réussi`
* If questionnaire is not valid redirect to uplad page but display reference of elements that had issues

### Element types
* questionnaire :  
  root element, its reference will be used in order to get JSON via GET `localhost:3000/questionnaires/:reference`.  
  Service validate presence of a reference and at least a content
* slide :  
  represents one "page" of questionnaire  
  validates, reference, label and one content presence at least
* text_input :  
  validates reference and label presence
* number_input :  
  validates reference and label presence
* boolean :  
  validates reference and label presence
* single_choice :  
  validates reference and label presence, and at least on response element
  response element validate value and label presence
* multiple_choice :  
  validates reference and label presence, and at least on response element
  response element validate value and label presence

### Questionnaire Validation
* form must be uploaded as YAML
* every questionnaire element should have a type
* only previously describe types are allowed
* Every element validate what is described previously

### Describe in README.md
* Data storage choices
* implementation of a versionning feature