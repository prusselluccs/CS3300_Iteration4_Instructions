# CS3300_Iteration4_Instructions
This guide is to set up integration testing for rails, adding test coverage with Simplecov, set up devise for rails, and set up circleci

### Step 1 - Change directories into your portfolio application <br>
`cd <Your application name>`

### Step 2 - Unit test for validating the presence of the description

1. Complete the unit test for validating the presence of the description <br>
   a. Make sure that you have code in the spec/models/projects.rb for testing the presence of the description <br>
   b. Run `rspec ./spec/models/projects.rb` and validate that the test fails <br>
   c. Add the appropriate code in the models/project.rb file to validate the presence of the description <br>
   d. Run `rspec ./spec/models/projects.rb` and validate that the test passed <br>

### Step 3 - Add scope specs to the spec/models/projects_spec.rb

```ruby
    require "rails_helper"

    RSpec.describe Project, type: :model do
    # ...
   
    context "scopes tests" do
         let(:params) { { title: "Title", description: "some description" } }
         before(:each) do
            Project.create(params)
            Project.create(params)
            Project.create(params)
         end

         it "should return all projects" do
            expect(Project.count).to eq(3)
         end
         
      end
   end
   ```
### Step 4 - Create functional test for Projects controller
1. Create Projects spec file: `spec/controller/projects_spec.rb` 

2. Add the following code to the `spec/controller/projects_spec.rb` file
```ruby
require 'rails_helper'

RSpec.describe ProjectsController, type: :controller do
    context "GET #index" do
        it "returns a success response" do
            get :index
            expect(response).to be_successful
        end
    end

    context "Get #show" do
        let!(:project) { Project.create(title: "Test title", description: "Test Description") }
        it "returns a success response" do
            get :show, params: { id: project }
            expect(response).to be_successful
        end
    end
end
   ```
### Step 5 - Create integration spec and a home page
1. Add capybara to `Gemfile` <br>
```ruby 
group :development, :test do
   gem 'capybara'
end
```
2. Make sure you are in your projects root directory
3. Create first feature test by running the following command <br>

   `bundle exec rails g rspec:feature home_page`


3. This will create spec/features/home_page_spec.rb file. <br>
   Now add the following code to this file
```ruby
require 'rails_helper'

RSpec.feature "Visiting the HomePage", type: :feature do
  scenario "The visitor should see projects" do
    visit root_path
    expect(page).to have_text("Projects")
  end
end
```
### Step 6 - Create Integration spec for Projects
1. In the project's root directory, run the command<br>

    `bundle exec rails g rspec:feature projects`


2. This will create spec/features/projects_spec.rb file. <br>
   Add the following code to this file
```ruby
require 'rails_helper'

RSpec.feature "Projects", type: :feature do
  context "Create new project" do
    before (:each) do
      visit new_project_path
      within("form") do
        fill_in "Title", with: "Test title"
      end
    end
  
    scenario "should be successful" do
      fill_in "Description", with: "Test description"
      click_button "Create Project"
      expect(page).to have_content("Project was successfully created")
    end
  
    scenario "should fail" do
      click_button "Create Project"
      expect(page).to have_content("Description can't be blank")
    end
  end

  context "Update project" do
    let(:project) { Project.create(title: "Test title", description: "Test content") }
    before(:each) do
      visit edit_project_path(project)
    end

    scenario "should be successful" do
      within("form") do
        fill_in "Description", with: "New description content"
      end
      click_button "Update Project"
      expect(page).to have_content("Project was successfully updated")
    end

    scenario "should fail" do
      within("form") do
        fill_in "Description", with: ""
      end
      click_button "Update Project"
      expect(page).to have_content("Description can't be blank")
    end
  end

## Add the following code but comment it out. We are working on getting this to work
#  context "Remove existing project" do
#    let!(:project) { Project.create(title: "Test title", description: "Test content") }
#    scenario "remove project" do
#      visit project_path('1')
#      click_link "Destroy this project"
#      expect(page).to have_content("Project was successfully destroyed")
#      expect(Project.count).to eq(0)
#    end
#  end
end
```
### Add Test Coverage with Simplecov
### Step 1 - Add simplecov gem
1. For the rails coverage report, run the following command in your project's root directory

    `bundle exec rails stats`

2. To install simplecov, add to your `gemfile` in the `group :test do` <br>
`gem 'simplecov'`
3. Run the command <br>
`bundle install`
4. Add the following code to `spec/rails_helper.rb`. You can add it at the end of the file<br>
```ruby
require 'simplecov'
Simplecov.start 'rails' do
    add_filter '/bin/'
    add_filter '/db/'
    add_filter '/spec/'   # for rspec
end
```
5. Add the following code to `spec/spec_helper.rb` at the end of the file<br>
```ruby
require 'simplecov'
SimpleCov.start

RSpec.configure do |config|

  config.expect_with :rspec do |expectations|
    expectations.include_chain_clauses_in_custom_matcher_descriptions = true
  end

  config.mock_with :rspec do |mocks|
    mocks.verify_partial_doubles = true
  end
end
```
6. Add the following code to the `config/environments/test.rb` file <br>
`config.eager_load = true`
7. In the `.gitignore` file, add the following code <br>
`/coverage`
8. Run a coverage report by typing in the following command <br>
`bundle exec rspec`
9. You can view the report by clicking on the `coverage` directory on the left pane and then right-clicking and downloading the file `index.html` <br>
10. You can then view the report in your browser.

### Rails - Devise User Authentication & CircleCI 
To setup the User Authentication and CircleCI, follow the instructions
using the URL below.

https://github.com/Olenderpj/CS-3300_Iteration4_Instructions.git