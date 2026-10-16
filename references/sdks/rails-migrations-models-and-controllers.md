# Rails migrations models and controllers

[Context index](activerecord-and-authentication-workflows.md) · [Setup and preceding context](rails-adapter-installation-and-configuration.md)

### Step 4: Create and run migrations

Generate a migration:

```
rails generate migration CreateProducts name:string description:text

```

Run the migration:

```
rails db:migrate

```

### Step 5: Define models

Define a model at `app/models/product.rb`:

```
class Product < ApplicationRecord
  validates :name, presence: true
end

```

### Step 6: Use in controllers

Your controllers can then call ActiveRecord methods against the Turso database:

```
class ProductsController < ApplicationController
  def index
    @products = Product.all
  end

  def create
    @product = Product.new(product_params)
    if @product.save
      redirect_to @product, notice: 'Product was successfully created.'
    else
      render :new
    end
  end

  private

  def product_params
    params.require(:product).permit(:name, :description)
  end
end

```

### Step 7: Execute raw SQL (if needed)

Raw SQL can be run as well:

```
results = ActiveRecord::Base.connection.execute("SELECT * FROM products")

```

### Examples

### Ruby on Rails App

See the full source code
