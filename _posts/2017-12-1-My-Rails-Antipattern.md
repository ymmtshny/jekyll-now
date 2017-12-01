---
layout: post
title: My Rails Antipattern!
---

# Use has_many through

Suppose that we have the following models.

```
class AClass  < ActiveRecord::Base
  has_many :b_classes  
end

class BClass  < ActiveRecord::Base
  has_many :c_classes  
end

class CClass  < ActiveRecord::Base
  belongs_to :bclass
end

```

Then, I wrote the following codes.

```
a = AClass.find(10)
a.b_classes.eahc do |b|
  b.c_classes.each do |c|
    puts c
  end
end

```

The above code could wrriten like the following.


```
class AClass  < ActiveRecord::Base
  has_many :b_classes  
  has_many :b_classes_c_classes, through: :b_classes, source: :c_classes
end

class BClass  < ActiveRecord::Base
  has_many :c_classes  
end

class CClass  < ActiveRecord::Base
  belongs_to :bclass
end

```

Now a record of AClass directly access to `c_classes` of each `b_class`. Notice that the record of `b_classes_c_classes` may include the same record. In this case we can use `distinct` to get records uniquely. 

```
a = AClass.find(10)
a.b_classes_c_classes.distinct.each do |c|
  puts c
end
```



