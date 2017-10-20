# Unit 1
## Chapter 4: Adding Images

In this chapter, you will add images.

### New Branch
Enter the command "git checkout -b 01-04-images".

### Header Image
* Download the header background image by entering the following LONG one-line command:
```
curl -o app/assets/images/header_background.png -OL https://raw.githubusercontent.com/jhsu802701/tutorial_rails_rubymn2/master/images/header_background.png
```
* Edit the file app/views/layouts/_header.html.erb.  Replace the line containing 'link_to "Ruby Users of Minnesota"' with the following:
```
    <%= image_tag 'header_background.png' %>
```
* In your web browser showing the local version of your app, hit refresh.  Now the header blocks the top of the page.
* To correct this, edit the file app/assets/stylesheets/custom.scss.  Under the body parameter, change the padding-top value to 260.
* In your web browser showing the local version of your app, hit refresh.  You should now see the header image.

### Replacing the Rails logo with the Ruby.MN logo
* Download the header background image by entering the following LONG one-line command:
```
curl -o app/assets/images/rumlogo.png -OL https://raw.githubusercontent.com/jhsu802701/tutorial_rails_rubymn2/master/images/rumlogo.png
```
* Edit the file app/views/static_pages/home.html.erb.  Replace the link_to element containing "rails.png" with the following line:
```
  <%= image_tag 'rumlogo.png' %>
```
* In your web browser showing the local version of your app, hit refresh.  You should now see the header image.

### Wrapping Up
* Enter the command "sh git_check.sh".  All tests should pass, and there should be no offenses.
* Enter the following commands:
```
git add .
git commit -m "Added widgets to the home page"
git push origin 01-04-images
```
* Go to the GitHub repository and click on the "Compare and pull request" button for this branch.
* When you see that your app passes in contiuous integration, accept this pull request to merge it with the master branch.
* Enter the following commands:
```
git checkout master
git pull
```
* Enter the command "sh heroku.sh".