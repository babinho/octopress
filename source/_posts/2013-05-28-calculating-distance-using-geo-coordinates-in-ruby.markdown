---
layout: post
title: "Calculating distance using geo coordinates in ruby"
date: 2013-05-28 12:39
comments: true
categories: 
---

As the social part of the sites is growing stronger, everyone of us has to
include a user's location of some sort into the application. There can be many
reasons for that, be it location based search, advertising or something else
unrelated. But mark this, you will have to deal with locations in the future.

I won't get into the details of how to get the user's latitude and longitude,
but [geocoder][geocoder] gem could be helpful for you.

I have found one JavaScript solution with the descriptions of what are the
different implementations of the calculation and which one is more correct so
I won't get into the explanation. You can check the explanation and even more
stuff you can do with 2 geo coordinates on the [Movable Type Scripts][movable-type]

The distance can be calculated using 3 formulas, __Haversine__, __Spherical
Law of Cosines__, and __Equirectangular approximation__

The prerequisites we will need are degree to radian conversion which is easily
done with `degree / 180 * Math::PI` but to make the code easier to write we
can monkey patch the Float with to_rad method which will calculate this for
us. We could use refinements, or make a method object and not pollute the
global space but we can leave it like this for now.

```ruby
class Float
  def to_rad
    self / 180 * Math::PI
  end
end
```

After we have done the prerequisites let's assume that we have two objects,
and each one has a latitude and a longitude. For the sake of this post we can
make them a hash with two keys `latitude` and `longitude`. And we take the
earth radius as 6371km

```ruby
class Geodistance
  include Math

  attr_reader :from, :to, :lat1, :lon1, :lat2, :lon2
  RADIUS = 6371

  def initialize(from, to)
    @from = from
    @to = to
    set_variables
  end

  def distance(type = 'haversine')
    begin
      self.send(type.to_sym)
    rescue
      raise NotImplementedError, 'The type you have requested is not implemented, try "cosines" or "approximation", or without params for "haversine"'
    end
  end

  private
  def haversine
    d_lat = (from[:latitude] - to[:latitude]).to_rad
    d_lon = (from[:longitude] - to[:longitude]).to_rad
    a = sin(d_lat / 2) * sin(d_lat / 2) + sin(d_lon / 2) *
        sin(d_lon / 2) * cos(lat1) * cos(lat2)
    c = 2 * atan2(sqrt(a), sqrt(1-a))
    RADIUS * c
  end

  def cosines
    acos(sin(lat1) * sin(lat2) +
         cos(lat1) * cos(lat2) *
         cos(lat2 - lat1)) * RADIUS
  end

  def approximation
    x = (lon2 - lon1) * cos((lat1 + lat2) / 2)
    y = lat2 - lat1
    sqrt(x * x + y * y) * RADIUS
  end

  def set_variables
    @lat1 = from[:latitude].to_rad
    @lat2 = to[:latitude].to_rad
    @lon1 = from[:longitude].to_rad
    @lon2 = to[:longitude].to_rad
  end
end
```

As you can see by calling the distance method with all 3 parameters, each one
will produce a slightly different result. As they say, the _haversine_ one
should be the most accurate, but take caution. I would like to benchmark them
some day and see which one calculates the result faster.

Thanks to [Movable Type Scripts][movable-type] for providing the JavaScript
code and all the insight. I just did a rewrote in Ruby.

[geocoder]: https://github.com/alexreisner/geocoder
[movable-type]: http://www.movable-type.co.uk/scripts/latlong.html
