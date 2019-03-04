---
layout: post
title:      "Sinatra Project"
date:       2019-03-04 02:08:36 +0000
permalink:  sinatra_project
---


For this project, I created a book club application. A member can sign up with an email and password, and then has access to create new books, see pages of books created by other members, and update or destroy a book she has created. The member can also review books, and update or destroy a review she created as well. <br><br>

I learned a lot from my last project that I tried to implement in having a smoother development experience:
* **Pseudocode** is something that I wish I did more of last time, so I tried harder to really just draw everything out before diving into the code. I laid out my models (Members, Books, Reviews and MemberBooks (which was a later addition)) on paper, and how they would relate to each other through ActiveRecord associations. I also drew out all of the tables I wanted in my database, so I could firmly decide what I wanted and would not have to make multiple migrations to make changes. I really felt like I had a clear understanding of where I wanted to start, and the direction I wanted to take, which helped immensely in keeping my head and my code clear. <br><br>
* **Asking for help** is something that I am still struggling with, but I've definitely reached out more. First off, shout out to my accountabilibuddy @meg_gutshall. Finding someone who you can either just code with or ask questions to, or answer questions for is so helpful in your learning and theirs. I learned so many things from her throughout this project, and solidified some concepts I was shaky in by just talking them through with her. 
<br>
### Throughout this project, I did a lot of research into things I was unfamiliar with, and I wanted to share one of the main and core concepts that I learned!<br><br>
* **Has_and_belongs_to_many** was an association that I was considering between my members and books because they "has_many" and "belongs_to" each other. A member has and belongs to many books and books have and belong to many members. 

```
class Member < ActiveRecord::Base
has_and_belongs_to_many :books
end

class Book < ActiveRecord::Base
has_and_belongs_to_many :members
end
```
What I ended up going with instead was
```
class Member < ActiveRecord::Base
has_many :member_books
has_many :book, through: :member_books
end

class Book < ActiveRecord::Base
has_many :member_books
has_many :members, through: :member_books
end

class MemberBook < ActiveRecord::Base
belongs_to :members
belongs_to :books
end
```
I chose this set of associations instead because this created the association within a join table (along with advice from a technical coach). By creating this table, we allow the option for modifications, validations or extra attributes within that join table, if necessary, that we would not have as easily if we went with the first option. Starting at the very beginning of a project, we have no idea how far our project will go, and what we might need it to do in the future. Because of this unknown, it's easier to spend a little more time when setting up associations by just going with the has_many :through from the beginning, so you won't have to worry about going all the way back to the start months/years from now and redesigning the associations when you need it to have another function. <br><br>
This project was a lot of fun, while frustrating at times, but I'm excited to move forward and dive into the world of Rails!

