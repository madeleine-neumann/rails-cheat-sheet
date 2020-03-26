### UsersController
```ruby
def index
  @users = User.all
  respond_to do |format|
    format.html
    format.csv { send_data @users.to_csv, filename: "users-#{Date.today}.csv" }
  end
end
```

### User Model
```ruby
def self.junk_leads_to_csv
  CSV.generate do |csv|
    csv << %w{ FirstName LastName Email Street Housenumber PostalCode City}
    # if you need information from another model: 
    # User.joins(:contract).map do |user|
    # contract = user.contract
    # end

    User.map do |user|
      csv <<  [user.first_name, user.last_name, user.email, user.street, user.house_number user.postalcode, user.city ]
    end
  end
end
```

### User Index View
```ruby
= link_to "User CSV Export", users_path(format: "csv"), class: "button"
```
