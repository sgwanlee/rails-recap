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

5.4 CRUD-Delete
```.destroy```

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