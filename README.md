# Working with web-tips

Here you can find a list of other demo projects that you can use as starting
point.

## Motivation

Use as your own tips repository. For example you have some script that you just
created to solve problem in projectA. Let's say tomorrow you need to solve
similar problem in projectB and day after that in projectC. You have to remember
where is the latest version that I created.
Solution is to push to your own repo, for example
`github.com/username/repo-name/my_script.rb` and put that as a comment in a
projectA, projectB and projectC
```
# github.com/username/repo-name/my_script.rb
class MyScript
```
so when you look at the file, you know where you can find latest version of it.

Also there might be a code that works in one project but does not in another, so
you want to create an example that works on both.

Naming your project is difficult since you could change the scope, but you can
start with `-tips` like `sidekiq-tips`. Later you can create more specific
example like `sidekiq-avoid-duplicated-jobs`

# Similar project

Another usefull source for learning is:

* https://github.com/eliotsykes/real-world-rails
* https://www.hexdevs.com/posts/massive-list-of-open-source-ruby-on-rails-applications-you-can-use-as-a-reference

# Begginer's guide

TODO: hello world using
https://lipanski.com/posts/smallest-docker-image-static-website

TODO: Deploy locally https://docs.dagger.io

# Tips how to create a good README

Use README.md as the only starting point (do not assume user has installed
anything like ruby, docker).

Use commands that user can quickly copy paste and see result.

Replace file inline (sed is good for one line insert, since you can also
copy/paste the line)

```
sed -i "" -e '/default from/c\
  default from: Rails.application.credentials.mailer_sender
' app/mailers/application_mailer.rb
```

Insert above certain line (sed for multilines needs `\` on all lines except last
line)
```
sed -i "" -e '/yield/i\
    <p class="notice"><%= notice %></p>\
    <p class="alert"><%= alert %></p>
' app/views/layouts/application.html.erb
```
Those commands from readme you can paste to shell.
You can use rails templates which call thor actions which are indempotent like
`insert_into_file` method with `before:` or `after:`
```
insert_into_file 'config/initializers/devise.rb', <<-RUBY, before: 'Devise.setup'
  ...
RUBY

insert_into_file 'config/initializers/devise.rb', <<-RUBY, after: "Devise.setup do |config|\n"
  ...
RUBY
```
You can create files or reference remote files using thor `get`
https://ruby-doc.org/stdlib-2.7.1/libdoc/bundler/rdoc/Bundler/Thor/Actions.html#method-i-get
```
file 'config/initializers/const.rb', <<'RUBY', force: true
RUBY

get "https://raw.githubusercontent.com/duleorlovic/rails_helpers_and_const/main/config/initializers/const.rb", "config/initializers/const.rb"
```

For inserting in rails credentials you can use
```
EDITOR='echo "mailer_sender: My Company <support@example.com>" >> ' rails credentials:edit
```

For adding `null: false` in migration file (since you do not know it's name you
need to use `EDITOR_FOR_GENERATOR`)

```
insert_into_file "config/application.rb", <<'HERE_DOC', before: /^  end$/

    # https://github.com/rails/rails/pull/31448#issuecomment-1399214463
    # Add `, null: false` on `t.` lines. Replace `jti` and `exp` with your names
    # EDITOR_FOR_GENERATOR='sed -i "" -r -e "/^[[:space:]]*t.*(jti|exp)$/ s/$/, null: false/"' rails g migration CreateJwtDenylist jti:index exp:datetime:index
    config.generators.after_generate do |files|
      if ENV["EDITOR_FOR_GENERATOR"]
        files.each do |file|
          system("#{ENV["EDITOR_FOR_GENERATOR"]} #{file}")
        end
      end
    end
HERE_DOC
```

# Link to you blog

If you have personal blog and you want README to show up there as well you can
```
ln -s ~/web-tips/terraform-fundamentals/README.md _posts/2021-12-30-terraform-fundamentals.md
```
when you deploy with `rake` the file is there.

# Provide your keys for hosting

Run your examples on your account.
For example to send sms you can use your example that sends sms (not sure where
to store secret for API).

# Other interesting ideas

[NOTE this could be solved with ClodWatch]
* create a service that users can contact with API to check if their service is
  working properly. For example they put in crontab every day, and if one day is
  missing, we send a notification to them.
  * sending emails
  * sending push notifications
