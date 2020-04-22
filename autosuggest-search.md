Pretending we have a search on our Landingpage for Jobs
We need to include JQuery UI and pg_search gems to our project:

### Gemfile
```ruby
gem 'pg_search'
gem 'jquery-ui-rails'
```
### application.js
```js
//= require jquery-ui
```

### Job Model
```ruby
class Job < ApplicationRecord
  include PgSearch::Model
  pg_search_scope :search_for_jobs, against: [:title], using: {
    tsearch: {prefix: true}
  }

end
```

### This is where the search results should be shown
```ruby
def index
  if params[:term]
    @searched_jobs = Job.search_for_jobs(params[:term])
  end
end
```

### jobs_controller.rb
```ruby
def job_search
  @jobs = Job.search_for_jobs(params[:term])
  render json: @jobs.map(&:title)
end
```

### routes
get "job_search", to: "jobs#job_search", as: "job_search"

## haml file for search
```ruby
= form_tag root_path, method: :get do
  = text_field_tag 'term', params[:term], placeholder: "Geben Sie den Job ein", id: "js-job-autocomplete", data: {autocomplete_source: job_search_path}
  = submit_tag 'Search!'
```

### JS File
```js
$(function() {
  $("#js-job-autocomplete").autocomplete ({
    source: $('#js-job-autocomplete').data('autocomplete-source'),
  })
})
```
