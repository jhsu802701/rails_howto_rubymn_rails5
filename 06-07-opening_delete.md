# Unit 6
## Chapter 7: Deleting Openings

In this chapter, you will provide the ability to delete job openings.  Only the owner of this object, a regular admin, or a super admin may have this ability.

### New Branch
Enter the command "git checkout -b 06-07-opening_delete".

### Part A: Controller Level

#### Controller Test
* Edit the file test/controllers/openings_controller_test.rb.  Add the following code immediately before the end of the definitions section:
```
  def delete_opening
    delete opening_path(@op4)
  end

  def delete_opening_disabled
    assert_no_difference 'Opening.count' do
      delete_opening
      assert_redirected_to root_path
    end
  end
```
* Edit the file test/controllers/openings_controller_test.rb.  Add the following code immediately before the last "end" statement:
```
  test 'should redirect delete when not logged in' do
    delete_opening_disabled
  end

  test 'should redirect delete when logged in as the wrong user' do
    sign_in @u1, scope: :user
    delete_opening_disabled
  end

  test 'should not redirect delete when logged in as the right user' do
    sign_in @u11, scope: :user
    assert_difference 'Opening.count', -1 do
      delete_opening
      assert_redirected_to user_path(@u11)
    end
  end

  test 'should not redirect delete when logged in as a regular admin' do
    sign_in @a4, scope: :admin
    assert_difference 'Opening.count', -1 do
      delete_opening
      assert_redirected_to user_path(@u11)
    end
  end

  test 'should not redirect delete when logged in as a super admin' do
    sign_in @a1, scope: :admin
    assert_difference 'Opening.count', -1 do
      delete_opening
      assert_redirected_to user_path(@u11)
    end
  end
```
* Enter the command "sh testc.sh".  All 5 new controller tests fail because of a missing route.

#### Routing
* In the file config/routes.rb, replace the line containing "resources :openings" with the following:
```
  resources :openings, only: [:show, :index, :create, :new, :update, :edit, :destroy] do
```
* Enter the command "sh testc.sh".  All 5 new controller tests fail because the destroy action is not defined in the opening controller.

#### Opening Controller
* Edit the file app/controllers/openings_controller.rb.  Add the following line just before the end of the before_action section:
```
  before_action :may_destroy_opening, only: [:destroy]
```
* Just before the end of the action section in app/controllers/openings_controller.rb, add the following code:
```
  def destroy
    uid = Opening.find(params[:id]).user_id
    Opening.find(params[:id]).destroy
    flash[:success] = 'Job opening deleted'
    redirect_to user_path(uid)
  end
```
* Just before the definition of opening_params, add the following code:
```
  def correct_user
    return false unless user_signed_in?
    current_user.id == Opening.find(params[:id]).user_id
  end
  helper_method :correct_user

  def may_destroy_opening
    return redirect_to(root_path) unless correct_user || admin_signed_in?
  end
  helper_method :may_destroy_opening
```
* Enter the command "sh testc.sh".  All tests should now pass.
* Enter the command "sh git_check.sh".  All tests should pass, and there should be no offenses.
* Enter the following commands:
```
git add .
git commit -m "Added opening delete (controller level)"
```

### Part B: View Level

#### Integration Test
* Enter the command "rails generate integration_test opening_delete".
* In the file test/integration/opening_delete_test.rb, replace everything between "class OpeningDeleteTest < ActionDispatch::IntegrationTest" and the last "end" statement with the following:
```
  def no_delete_button
    visit opening_path(@op4)
    assert page.has_no_link?('Delete Job Opening', href: opening_path(@op4))
  end

  def gets_delete_button
    visit opening_path(@op4)
    assert page.has_link?('Delete Job Opening', href: opening_path(@op4))
  end

  def can_delete
    gets_delete_button
    assert_difference 'Opening.count', -1 do
      click_on 'Delete Job Opening'
    end
    assert_text 'Job opening deleted'
    assert page.has_css?('title', text: full_title('User: Jackie Gleason'),
                                  visible: false)
    assert page.has_css?('h1', text: 'User: Jackie Gleason',
                               visible: false)
  end

  test 'visitor does not get the delete button' do
    no_delete_button
  end

  test 'wrong user does not get the delete button' do
    login_as(@u1, scope: :user)
    no_delete_button
  end

  test 'right user gets the delete button' do
    login_as(@u11, scope: :user)
    gets_delete_button
  end

  test 'regular admin gets the delete button' do
    login_as(@a4, scope: :admin)
    gets_delete_button
  end

  test 'super admin gets the delete button' do
    login_as(@a1, scope: :admin)
    gets_delete_button
  end

  test 'right user can delete opening' do
    login_as(@u11, scope: :user)
    can_delete
  end

  test 'regular admin can delete opening' do
    login_as(@a4, scope: :admin)
    can_delete
  end

  test 'super admin can delete opening' do
    login_as(@a1, scope: :admin)
    can_delete
  end
```
* Enter the command "sh test_app.sh".  The last 6 of your new integration tests fail.
* Enter the command "alias test1='command for running failed tests minus the TESTOPTS portion'".
* Enter the command "test1".  The same 6 tests fail because the delete button is missing.

#### Adding the Delete Button
* Edit the file app/views/openings/show.html.erb. Immediately after the edit button, add the following code:
```
<% # BEGIN: delete opening button %>
<% @correct_user = false %>
<% if user_signed_in? %>
  <% @correct_user = true if current_user.id == Opening.find(params[:id]).user_id %>
<% end %>
<% if @correct_user || admin_signed_in? %>
  <%= link_to "Delete Job Opening", @opening, method: :delete,
                                              data: { confirm: "Are you sure you wish to delete this opening?" },
                                              class: "btn btn-danger"
  %>
  <br><br>
<% end %>
<% # END: delete opening button %>
```
* Enter the command "test1".  All tests should pass.
* Enter the command "sh git_check.sh".  All tests should pass, but Rails Best Practices flags app/views/openings/show.html.erb and tells you that you should move the logic into the controller.
* In the file config/rails_best_practices.yml, add the file app/views/openings/show.html.erb to the list of files to be ignored by MoveCodeIntoControllerCheck.
* Enter the command "sh git_check.sh".  All tests should pass, and there should be no offenses.

### Wrapping Up
* Enter the following commands:
```
git add .
git commit -m "Added opening delete (view level)"
git push origin 06-07-opening_delete
```
* Go to the GitHub repository and click on the "Compare and pull request" button for this branch.
* When you see that your app passes in continuous integration, accept this pull request to merge it with the master branch.
* Enter the following commands:
```
git checkout master
git pull
```
