## find records where column is null or empty
```ruby
User.where(second_name: [nil, ""])
```

## find records where column is not null or empty
```ruby
User.where.not(second_name: [nil, ""])
```
