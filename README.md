Formbuilder.rb [![circle ci status](https://circleci.com/gh/dobtco/formbuilder-rb.png?circle-token=a769ad2fc81271bc1869b5e5a95053efa36b376f)](https://circleci.com/gh/dobtco/formbuilder-rb) <a href='https://coveralls.io/r/dobtco/formbuilder-rb'><img src='https://coveralls.io/repos/dobtco/formbuilder-rb/badge.png' alt='Coverage Status' /></a>
========

Formbuilder.rb is a [Rails Engine](#) that's designed as a compliment to [Formbuilder.js](https://github.com/dobtco/formbuilder), a library that lets your users create their own webforms inside of your application.

Since Formbuilder.rb is a fairly non-trial piece of software, it's important to understand its components and how it works:

1. We add `ResponseField`, `EntryAttachment`, and `Form` models to your application.  (Each type of response field (text, checkboxes, dropdown, etc.) uses [STI](), inheriting from the `ResponseField` model.)
3. You `include Formbuilder::Entry` in an existing model.
4. We add a few classes to help you render forms and entries.

*Note: All Formbuidler models and classes are namespaced within the `Formbuilder` module.*

If you have a few moments, consider reading the source, especially the Rails app in `spec/dummy`, as it should give you a good idea of how Formbuilder integrates.

### Installation
#### 1) Create the migrations for the Formbuilder models
1. `rake formbuilder:install:migrations`
2. `rake db:migrate`

#### 2) The `Entry` model gets mixed in to an existing model in your application
```ruby
#  submitted_at    :datetime
#  responses       :hstore
#  responses_text  :text

class Entry < ActiveRecord::Base

  include Formbuilder::Entry

end
```

#### 3) Associate a form with an existing model (optional)
```ruby
class MovieTheater < ActiveRecord::Base

  has_one :form, as: :formable

end
```

### Usage

#### Render a form
```erb
<%= Formbuilder::FormRenderer.new(@form, @entry).to_html %>
```

#### Save an entry
```ruby
@entry = Entry.new(form: @form)
@entry.save_responses(params[:response_fields], @form.response_fields) # validates automatically
```

#### Validate an entry
```ruby
@entry.valid?
# => false

@entry.error_for(form.response_fields.first)
# => "can't be blank"
```

#### Integrate with the [Formbuilder.js](https://github.com/dobtco/formbuilder) frontend
```ruby
# config/routes.rb
resources :forms, only: [:update]

# app/controllers/forms_controller.rb
class FormsController < Formbuilder::FormsController
  ...
end
```

#### License

MIT
