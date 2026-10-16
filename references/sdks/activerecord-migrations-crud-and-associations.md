# Activerecord migrations crud and associations

[Context index](activerecord-and-authentication-workflows.md) · [Setup and preceding context](activerecord-credentials-and-installation.md) · [Previous phase](activerecord-connections-and-models.md)

### Step 5: Create and execute a migration

Add a migration file, such as `001_create_products.rb`:

```
class CreateProducts < ActiveRecord::Migration[8.0]
  def change
    create_table :products do |t|
      t.string :name
      t.text :description
      t.timestamps
    end
  end
end

```

Execute the migration:

```
require_relative 'database'
require_relative '001_create_products'

CreateProducts.migrate(:up)

```

### Step 6: Execute

Perform some basic CRUD operations:

```
product = Product.create(name: 'Book', description: 'A book about books')

```

### Step 7: Work with associations

Define associations in your models:

```
class Author < ActiveRecord::Base
  has_many :books
end

class Book < ActiveRecord::Base
  belongs_to :author
end

```

Use associations in your code:

```
author = Author.create(name: 'Jane Doe')
book = author.books.create(title: 'My First Book')

puts author.books.count # => 1
puts book.author.name # => "Jane Doe"

```
