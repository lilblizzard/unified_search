# unified_search

Given any query string, aggregate response data from two separate endpoints into a single JSON response for consumption by a front end application. Extract up to 10 results from each endpoint and combine into a single JSON structure.

To run the script:

1. Clone this repo and `cd` into the directory
2. Run `bundle install` 
3. Run `bundle exec ruby unified_search.rb`
4. Enter a search query when prompted!

This script functions in a couple of discreet steps.

1. Call both endpoints with given search query and parse those responses into Ruby hashes
2. Extract the necessary and available data from the Colenda/Finding Aids response hashes into new, separate formatted hashes
3. Combine those hashes in to a single hash and sort by most recent date
4. Convert our final hash to a JSON response and return to user

Surprisingly, the most complicated part of the process was extracting the date from each entry. Because I have to sort by date, I must have a single four-digit number in each search result. Since the data is coming from many different sources, this is not as easy as just grabbing the field and moving on.

```ruby
def extract_date date_string
  date_array = date_string.split(/[\s,-]/)
  date_int_array = []
  date_array.each do |d|
    date_int_array << d.to_i unless d.to_i < 100
  end
  date_int_array.min
end
```

I start by taking the input date string and splitting it into an array using spaces and dashes as delimiters. I then loop through that array and add values that are greater than 100 to a new array. I assume here that we do not have any items in our search that have a date before 100. Finally, I take the minimum value of that final array to ensure we get the earliest year that occurs in the input date field.

Another assumption that I make is that fields like `title_tesim` and `title_ssim` will always be the same in Colenda. I’ve defaulted to using data from the `tesim` field because it occurs first. In the event that these values are different, we could potentially miss out on important data. A next step to improve the safety of this part of the response would be to get both values, compare them, return the field if they are the same, and combine them into a single string if they are different. In my evaluation of the available data, I didn’t find any values in these similar fields that were different.