# Nesting Attributes in Rails Apps 🪆
In this lesson, we will cover the concept of nesting attributes in Rails applications. We will explore `accepts_nested_attributes_for`, providing guidelines on when to use them and examples to illustrate usage.

## Introduction to Nested Attributes
Nesting is a way of organizing and structuring your code to reflect the hierarchical relationships between different resources. In Rails, nesting can be applied to models to create more intuitive and manageable applications. Nested attributes allow you to save attributes on associated records through the parent record. This feature is especially useful when dealing with forms that need to update multiple models.

### When to Use Nested Attributes
- **Complex Forms**: When you have a form that needs to handle attributes for multiple related models.
- **Efficient Updates**: To update parent and child records in a single request.

### Example of Nested Attributes
Here’s how you can set up nested attributes in a Rails application:

```ruby
# app/models/project.rb
class Project < ApplicationRecord
  has_many :tasks, dependent: :destroy
  accepts_nested_attributes_for :tasks, allow_destroy: true
end
```

With this setup, you can manage `Task` records through the `Project` model. For example, in your `ProjectsController`, you can permit nested attributes for tasks:

```ruby
# app/controllers/projects_controller.rb
class ProjectsController < ApplicationController
  def new
    @project = Project.new
    @project.tasks.build
  end

  def create
    @project = Project.new(project_params)
    if @project.save
      redirect_to @project, notice: 'Project was successfully created.'
    else
      render :new
    end
  end

  def edit
    @project = Project.find(params[:id])
  end

  def update
    @project = Project.find(params[:id])
    if @project.update(project_params)
      redirect_to @project, notice: 'Project was successfully updated.'
    else
      render :edit
    end
  end

  private

  # note: arrays need to be the last argument  
  def project_params
    params.require(:project).permit(:name, :description, tasks_attributes: [:id, :name, :description, :due_date, :_destroy])
  end
end
```

### Form with Nested Attributes
Here’s an example of a form that handles nested attributes:

```erb
<!-- app/views/projects/_form.html.erb -->
<%= form_with(model: @project) do |form| %>
  <div class="field">
    <%= form.label :name %>
    <%= form.text_field :name %>
  </div>

  <div class="field">
    <%= form.label :description %>
    <%= form.text_area :description %>
  </div>

  <h3>Tasks</h3>
  <%= form.fields_for :tasks do |task_form| %>
    <div class="nested-fields">
      <div class="field">
        <%= task_form.label :name %>
        <%= task_form.text_field :name %>
      </div>
      <div class="field">
        <%= task_form.label :description %>
        <%= task_form.text_area :description %>
      </div>
      <div class="field">
        <%= task_form.label :due_date %>
        <%= task_form.date_field :due_date %>
      </div>
      <%= task_form.check_box :_destroy %>
      <%= task_form.label :_destroy, "Remove task" %>
    </div>
  <% end %>

  <div class="actions">
    <%= form.submit %>
  </div>
<% end %>
```

## Guidelines for Using Nested Attributes
- **Use for Complex Forms**: Use nested attributes for forms that need to handle multiple models.
- **Manage Dependencies**: Ensure dependent attributes are managed correctly, especially when allowing destruction of nested records.

## Quiz

- Which method is used to allow nested attributes in a Rails model?
- `accepts_nested_attributes_for`
  - Correct! This method is used to enable nested attributes for associations.
- `has_many`
  - Not correct. has_many defines a one-to-many association.
- `belongs_to`
  - Not correct. belongs_to defines a one-to-one association.
{: .choose_best #nested_attributes_method title="Method for Nested Attributes" points="1" answer="1"}

- The `accepts_nested_attributes_for` method allows you to save attributes on associated records through the parent record.
- True
  - Correct! This method is used for handling nested attributes.
- False
  - Not correct. `accepts_nested_attributes_for` is indeed used for this purpose.
{: .choose_best #nested_attributes_true_false title="Accepts Nested Attributes" points="1" answer="1"}

## Conclusion
Nesting in Rails, through `accepts_nested_attributes_for`, provides a powerful way to manage complex relationships and forms in your application. By following the guidelines and examples provided, you can implement these features effectively to create intuitive and maintainable  applications.

Happy coding! 🚀
