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
```
get "job_search", to: "jobs#job_search", as: "job_search"
```

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
    // You need this line only, if you would like to scope the styles
    }).autocomplete("widget").addClass( "jobsearch" );
})
```

### Basic ugly styling
```sass
.jobsearch
  background-color: white
  box-shadow: 1px 0px 8px rgba(0,0,0,.3)
  max-width: 500px
  margin-top: 30px

  // JqueryUI Element Styles
  .ui-menu-item
    background-color: white
    border-bottom: 1px solid $brand-color
    cursor: pointer

    &:last-child
      border-bottom: none

    &:hover
      background-color: $brand-color
      color: $white

  .ui-menu-item-wrapper
    +font-regular
    +fontsize-l
    padding: $default-margin/2

  .ui-menu-item-wrapper
    border-bottom: 1px solid $brand-color

    &:last-child
      border-bottom: none

```
