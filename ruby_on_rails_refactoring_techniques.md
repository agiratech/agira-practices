# Rails Refactoring Techniques

# Objectives
1. DRY(Don't repeat yourself)
2. Clarity
3. Easy to find
4. Easy to change
5. Easy to test
6. Performance tuning

# Guidelines
Sandi Metz' four rules from Ruby Rogues episode 87. Check this: https://robots.thoughtbot.com/sandi-metz-rules-for-developers

* Your class can be no longer than 100 lines of code.
* Your methods can be no longer than five lines of code.
* You can pass no more than four parameters and you can't just make it one big hash.
* When a call comes into your Rails controller, you can only instantiate one object to do whatever it is that needs to be done. And your view     can only know about one instance variable.

# Patterns & Techniques
There are few patterns and techniques to refactor rails models, controllers and views. Some main techniques are

1. [Concerns](https://github.com/agiratech/agile-practices/blob/master/ruby_on_rails_refactoring_techniques.md#what--is-concern)
2. Services objects
3. Decorators
4. Presenters

# What  is concern?
* Concerns are pieces of code that allow you to better organize the code that     you write.
* Default part of Rails 4, not an extra gem. But it should apply equally to     3.x.x and earlier.
* Works not just for models, but also controllers.
* Not just for code shared across models, but super to simply divide a large model file into logical chunks with associated tests

# Sample
```
module ModuleName
    extend ActiveSupport::Concern

     included do
         # your class macros
     end

     # place your instance methods here
     module ClassMethods
     # place class methods here, removing self.
     end
end
```

# Why use concern?
* Your model file is huge, and your matching spec is even larger.
* You 've got duplicated code in several models
* You want a super easy and clean way to put this code into a single module containing:
  * Instance methods
  * Class methods
  * Class macros (has_many, belongs_to, validates, scope, etc.)
  
 # How to discover domain?
 * Discover a set of related behavior in a model. Possibly, this behavior is in     multiple models. Prefer to discover a "domain" rather than some "technical" aspect. A domain grouping is like Taggable. A technical grouping would be like Finder Methods or "Validation Methods"
 * Create a file to hold the concern module: 
   * If just one concern for model, or if shared among models, then in app/models/concerns.
   * If multiple concerns for single model, group in subdirectory under models, such as app/models/user/.
 * In the new module, underneath the module declaration, place extends     ActiveSupport::Concern.  It tells rails that we are creating a concern
 * Discover a set of related behavior in a model. Possibly, this behavior is in     multiple models. Prefer to discover a "domain" rather than some "technical" aspect. A domain grouping is like Taggable. A technical grouping would be like Finder Methods or "Validation Methods"
 * You've got duplicated code in several models
 
 # Steps
 1. Create file for Concern with skeleton structure in /apps/models/concerns/module_name    
 2. Move instance methods to module
 3. Move class macro code to included block.
 4. Move class methods to inside of module ClassMethods.
 5. Place include statement in original model class:
    * Include ModuleName (or)
    * include ModelName::ModuleName
    
 # Example
 ```
 module Searchable
  extend ActiveSupport::Concern

  included do
    include Elasticsearch::Model
    include Elasticsearch::Model::Callbacks
    index_name 'articles_index' #create index name

    mapping do
      indexes :id, index: :not_analyzed
      indexes :title, analyzer: 'snowball'
      indexes :description, analyzer: 'snowball'
      indexes :created_at, :type => 'date'
    end
  end

  module ClassMethods
    def search(params)
      search_definition = {
        sort: [created_at: {order:'desc'}],
      }
      search_definition[:query] = {
        query_string: {
          query: params[:q]
        }
      } if params[:q].present?

      __elasticsearch__.search(search_definition)
    end
  end
 
end
 ```
 
 # Advantages
 * Ease and safety of refactoring. Concerns are a great first refactoring step because using concerns involves simply moving the methods and tests into separate files. And code accessing those methods does not need to change.
 * A core part of Rails 4, so one can expect familiarity among Rails developers.
 * Simplicity. It's just a code organization that makes it easier to navigate to the right source and test file. Simpler than plain Ruby methods of include and extend.
 * Can DRY up code when a concern is shared among multiple models or controllers
 
 # Notes
 Applies to controllers as well as models, although with controllers, on can break up a controller into several controllers that are referenced in the routes.rb file, so you may not need to use a Concern for simply reducing the size of a controller. In the controller case, concerns are useful for sharing code.
 
