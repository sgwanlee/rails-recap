# rails-recap

###[Active Record](http://guides.rubyonrails.org/active_record_basics.html)

1.1 Object가 Data와 Behavior를 가지고 있음. Object가 Data access logic도 포함함.

1.2 Object-Relational Mapping(ORM). SQL없이도 손쉽게 database에 저장/찾을 수 있음.

1.3 database 작업을 Object-oriented로 할 수 있음

2.1 Rails는 class명을 복수화해서 database에서 찾는다. (i.e. Book --> books)
두 단어로 이뤄진 class명은 ruby convention을 따르고 (CammelCase), table명은 두 단어를 underscore로 분리한 것으로 쓴다.(i.e. BookClub --> book_clubs)

나름 강력하다는 pluralization 기능은 class명이 Person이면 table명은 people이어야 한다.

2.2 Foreign key는 단수화된 class명 + id로 이름 지어야 한다. (i.e Book --> book_id)

Primary key는 id를 기본값으로 사용한다.
Optional이지만 미리 reserved되어있는 column명도 있다.
created_at, updated_at, lock_version, type, (association_name)_type, (table_name)_count

3.1 Active Record model은 만드는 쉬운 방법은 ```ActiveRecord::Base``` class를 상속받는 것이다.

4.1 legacy database를 사용할 때는? Naming convention을 사용하지 않을 때는 ```self.table_name```을 override해준다.

primary_key가 'id'가 아닐 경우에는 ```self.primary_key```를 override해준다.

5.1 CRUD-Create
new와 create모두 초기화를 위해 새로운 object를 block에다가 yield시킬 수 있다.

```
user = User.new do |u|
	u.name = "David"
	u.occupation = "Code Artist"
	end
```

5.2 CRUD-Read
```.all```, ```.first```, ```.find_by(<column_name>:)```, ```.where```

5.3 CRUD-Update
bulk update --> ```.update_all```

5.4 CRUD-Delete ```.destroy```

6.1 ```.save```, ```.update```에서 validation이 진행됩니다.
bang counterparts ( ```.save!```, ```.update!```)는 validation에 실패하면 exception을 발생시킵니다.

7.1 Callback들은 CRUD 이벤트 전후에 원하는 코드를 넣을 수 있게 합니다.


###[Active Record Migration](http://guides.rubyonrails.org/active_record_migrations.html)

1.1 primary key인 `id`는 migration 파일에 적지 않아도 자동으로 생성된다.
timestamps는 ```created_at```과 ```updated_at``` column을 만든다.

1.2 migration 파일에서 뭔가 한다면, ```change``` method가 아닌 ```up```, ```down``` method를 직접 구현해야 한다.

2.1 migration file의 이름은 <UTC timestamp> + <'_'로 구분지어진 migration class name>이다. migration class는 CammaelCase로.

migration 이름이 "AddXXXToYYY" 이고 뒤에 column list가 따라오면, Rails가 자동으로 ```add_column``` method를 추가해준다. 

새로 추가될 column에 :index를 붙이면, ```add_index``` method를 추가해준다.

```
$ bin/rails generate migration AddPartNumberToProducts part_number:string:index
```

migration 이름이 "CreateXXX"에다 뒤에 column list가 따라오면 Rails가 자동으로 ```create_table``` method를 추가해준다.

column type을 ```reference```로 하면 ```add_reference``` method가 추가된다. 이 method는 foreing key를 추가한다.


2.2
2.3

######3 Writing a Migration
3.1 ```create_table```

3.2 ```create_join_table```

```
create_join_table :products, :categories
```
이 migration은 categories_products table을 만들고, category_id, product_id 두 개의 column을 만든다.

3.3 ```change_table```

3.4
3.5
3.6

```
add_foriegn_key :articles, :authors
```
*articles* table에 *author_id* column을 만든다. *author* table의 *id* column을 참조한다. 

3.7
Active Record에서 제공하는 helper method로 부족하면, 직접 SQL을 실행시킬 수 있다.

```
Product.connection.execute('UPDATE `products` SET `price`=`free` WHERE 1')
```

3.9 복잡한 migration일 때는 rollback function을 직접 적어준다.
[using reversible](http://guides.rubyonrails.org/active_record_migrations.html#using-reversible)

3.10

3.11
```revert`` method를 통해서 이전 migration을 roll-back할 수 도 있음

######4 Running migration
특정 version까지 migration

```
$ bin/rake db:migrate VERSION=20080906120000
```

4.1
```$ bin/rake db:rollback STEP=3```
STEP으로 몇개의 migration을 rollback할지 정할 수 있다.
```$ bin/rake db:redo STEP=3```
redo도 마찬가지

4.2

4.3
```db:setup``` --> 새로 만들기
```db:reset``` --> drop하고 다시 새로 만들기
```db:drop``` --> drop하기

4.4
다른 환경에서 migration

```
$ bin/rake db:migrate RAILS_ENV=test
```

######5 이미 있는 migration 고치기
rollback하고 고치고 다시 migrate를 하면된다. 하지만 그리 좋은 방법은 아니다. 같이 일하는 사람들이 골치아파 진다. 새로운 migration 파일을 만들는게 좋다.

######6 Schema
[annotate_models](https://github.com/ctran/annotate_models) gem은 각 모델 파일에 schema를 자동으로 작성해준다.

schema format을 ```config/application.rb``` 의 ```config.active_record.schema_format``` 에서 :ruby 또는 :sql로 변경할 수 있다. 
######8. Seed
Migration으로 seed data를 넣을 수도 있지만, Rails엔 ```seeds``` feature가 있음

```
db/seeds.rb

5.times do |i|
  Product.create(name: "Product ##{i}", description: "A product.")
end
```

```
rake db:seed
```
