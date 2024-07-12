# EasyBroker

A gem that makes it easy to work with the [EasyBroker API](https://api.easybroker.com/).

## Installation

Add this line to your application's Gemfile:

```ruby
gem 'easy_broker'
```

And then execute:

    $ bundle

Or install it yourself as:

    $ gem install easy_broker

## Usage

Configure the gem with your application ID and secret key. This is the first draft which
just offers basic support for finding and searching properties.

```ruby
EasyBroker.configure do |config|
  config.api_key = 'your_app_key'

  # Optionally change the root API URL
  # config.api_root_url = EasyBroker::STAGING_API_ROOT_URL

  # As an integration partner you must configure the country code
  # config.country_code = 'MX'

  # Get the information from the organizations connected to your site using a partner code
  # config.use_partner_code = true
end
```

Then in your app use the _client_ to access the API.

```ruby
client = EasyBroker.client

client.properties.find('EB-123')
results = client.properties.search # results is an enumerator

# Print the first page of up to 20 results
results.each do |property|
  puts property.public_id
end

# Or use find_each to automatically iterate over all the results on each page
results.find_each do |property|
  puts property.public_id
end

# The total number of results of all pages
results.total 

# The current page
results.page 

# Loads the next page results
results.next_page 


# Search for only published properties
client.properties.search(search: { statuses: [:published] }, limit: 1, page: 1)

# As a partner you can update the property integration on EB
client.integration_partners.property_integrations.update('EB-123', body: { status: 'successful', listing_url: "https://www.yourwebsite.com/EB-XXXX01" })
```

You can also pass a logger to log any methods that make remote calls. The logger class must implement a `log` method which will be called with the [HTTParty response](https://www.rubydoc.info/github/jnunemaker/httparty/HTTParty/Response) for every remote request sent.

```ruby
EasyBroker.client(logger: my_logger)
```

## Available Endpoints


```ruby
client.locations # Find geographic locations
client.contact_requests # List and search contact requests in your account - TDB create via post
client.properties # List, search and find properties in your account
client.mls_properties # List, search and find properties in the MLS - requires MLS API Plan
client.listing_statuses # List and search the listing status for properties. Great for syncing large sets of properties. - includes MLS properties if you have the MLS Plan

### The following require a partner api key.
client.integration_partners.agencies # List and search connected agencies.
client.integration_partners.agents # View detailed agent contact information for those agents assigned to properties.
client.integration_partners.listing_statuses # List and search the listing status for partner properties.
client.integration_partners.properties # View the full property listing and its details.
client.integration_partners.property_integrations # Update the property integration on EB with the listing status on your website.
```

## Development

After checking out the repo, run `bin/setup` to install dependencies. Then, run `rake test` to run the tests. You can also run `bin/console` for an interactive prompt that will allow you to experiment.

To install this gem onto your local machine, run `bundle exec rake install`. To release a new version, update the version number in `version.rb`, and then run `bundle exec rake release`, which will create a git tag for the version, push git commits and tags, and push the `.gem` file to [rubygems.org](https://rubygems.org).

## Contributing

Bug reports and pull requests are welcome on GitHub at https://github.com/easybroker/easy_broker_api.

## License

The gem is available as open source under the terms of the [MIT License](https://opensource.org/licenses/MIT).
