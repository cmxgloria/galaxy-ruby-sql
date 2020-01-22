# galaxy-ruby-sql
```
//main.rb
     
require 'sinatra'
require 'sinatra/reloader'
require 'pry'
require 'pg'

def run_sql(sql)
  conn = PG.connect(dbname: 'galaxy_app')
  result = conn.exec(sql)
  conn.close
  return result 
end

get '/' do
  @result = run_sql("select * from galaxy;")
  erb :index
end

get '/galaxy/new' do
  erb :new_galaxy
end

get '/galaxy/:id' do
  sql = "SELECT * FROM galaxy WHERE id = #{params[:id]};"
  @galaxy = run_sql(sql).first
  erb :details
end

post '/galaxy' do
  sql = "insert into galaxy (name, image_url) values ('#{params[:name]}', '#{params[:image_url]}');"
  run_sql(sql)
  redirect '/'
end

delete '/galaxy/:id' do
  sql =" delete from dishes where id = #{params[:id]};"
  run_sql(sql)
  redirect '/'
end

get 'galaxy/:id/edit' do
  sql = "select * from galaxy where id = #{params[:id]};"
  @galaxy = run_sql(sql)[0]
  erb :edit_galaxy
end

patch '/galaxy/:id' do
  sql = "update * from galaxy set name = '#{params[:name]}', image_url = '#{params[:image_url]}' where id = #{params[:id]};"
  run_sql(sql)
  redirect "/galaxy/#{params[:id]}"
end

//schema.sql
CREATE DATABASE galaxy_app;

CREATE TABLE galaxy (
    id SERIAL PRIMARY KEY,
    name VARCHAR(200),
    image_url VARCHAR(650)
);

INSERT INTO galaxy (name, image_url)
VALUES ('Earth', 'https://i.guim.co.uk/img/media/3cd2819e1714a52d584e9b3c12a565e44b6c9b0c/0_315_3749_2250/master/3749.jpg?width=700&quality=85&auto=format&fit=max&s=45f2f265a4cc6486d41c329db1a828f5');

INSERT INTO galaxy (name, image_url)
VALUES ('Mars', 'https://upload.wikimedia.org/wikipedia/commons/thumb/0/02/OSIRIS_Mars_true_color.jpg/1200px-OSIRIS_Mars_true_color.jpg');

INSERT INTO galaxy (name, image_url)
VALUES ('Venus', 'https://cdn.mos.cms.futurecdn.net/dzxtQ2dXH9SVKztJTbAXSA-320-80.jpg');



//details.erb
<h1> <%= @galaxy['name'] %> details </h1>
<a href="/galaxy/<%= @galaxy['id'] %>/edit">Edit</a>
<form action="/galaxy/<%= @galaxy['id'] %>" method="post">
  <input type="hidden" name="_method" value="delete">
  <button>delete</button>
</form>

//edit_galaxy.erb
<h1>Edit Galaxy</h1>
<form action="/dishes/<%= @galaxy['id'] %>" method="post">
<%# sinatra support patch, change to post %>
  <input type="hidden" name ="_method" value="patch">
  <label for="">name</label>
  <input type="hidden" name ="name" value="<%= @galaxy['name']%>">
  <label for="">image url</label>
  <input type="text" name="image_url" value="<%= @galaxy['image_url']%>">
  <button>Update</button>
</form>

//index.erb
<h1>Galaxy</h1>
<a href="/galaxy/new">post new planet</a>
<br>
<% @result.each do |galaxy| %>
<img src="<%= galaxy['image_url']%>" alt="">
<h2>
<a href="/galaxy/<%= galaxy['id']%>">
<%= galaxy['name']%>
</a>
</h2>
<%end%>

//layout.erb
<!doctype html>
<html lang="en">
<head>
  <meta charset="UTF-8">
  <title>Hello World</title>
  <link rel='stylesheet' href='/stylesheets/main.css'>
  <meta name="viewport" content="width=device-width, initial-scale=1.0">
</head>
<body>

  <%= yield %>

</body>
</html>

//new_galaxy.erb
<h1>Create new galaxy</h1>
<form action="/galaxy" method="post">
  <label for="">name</label>
  <input type="text" name ="name">
  <label for="">image url</label>
  <input type="text" name="image_url">
  <button>Create</button>
</form>




```
