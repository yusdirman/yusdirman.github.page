---
layout: post
title:  "Rails Nested Form with Multiple Child and Multiple Attributes Assign to Params"
date:   2017-10-23 13:05:00 +0800
categories: Ruby on Rails
---

# Ruby on Rails Nested Form : Multiple Child and with custom parameters to assign to params before update or create



I have two models with has_many, belongs_to associations and both has association with `User` model to record the user created the record. This two models has nested form attributes enabled.


```ruby

  # model request
  class Request < ApplicationRecord
    has_many :items, dependent: :destroy, inverse_of: :item_request, autosave: true
    accepts_nested_attributes_for :items, reject_if: :all_blank, allow_destroy: true
    belongs_to :user
  end

  # model Items
  class Items < ApplicationRecord
    belongs_to :request, inverse_of: :request_items
    belongs_to :user
  end
```

Before `save` or `update` in method `create` and `update`, the application should set user as `@current_user`, a global variable set after `session#create`

But, as `Items` is set as multiple items from the nested form, the controller need to iterate each items senf by the Form


In my `requests_controller` I did this:

```ruby
def item_request_params
    # iterate each value in params to insert
    params[:request][:items_attributes].each do |r, data|
      params[:item_request][:request_items_attributes][r][:user_id] = @current_user.id
    end

    # then permit it
    params.require(:request).permit(:department_id, :extension, :doctor, :request_status_id, :is_approved, :approved_by, :approval_date, items_attributes:
      [:id, :item, :quantity, :is_approved, :unit_price, :total_price, :user_provider, :user_provider_alternate, :created_by])
      # use merge with single array params
      .merge(created_by: @current_user.id)

  end

```


By default, to merge attributes into any single array params, use `.merge` normally.


Thanks..


yusdirman
