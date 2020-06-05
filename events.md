# Events
## As a speaker
{% assign events = site.events| sort: 'eventDate' | reverse %}
{% for event in events %}
  - [{{event.title}}]({{event.url}})  
    {{event.eventDate|date_to_long_string: "ordinal", "US"}}  
{% endfor %}
- WordPress Meetup Leiden  
  December 3rd, 2019

- WordCamp Nijmegen  
  September 21st, 2019

- WordCamp Nijmegen  
  September 1st, 2018

## As a visitor
- WordCamp Europe 2020 (online)
- FOSDEM 2020
- WordCamp US 2019 (St. Louis)
- WordCamp Nijmegen 2019
- WordCamp Europe 2019 (Berlin)
- WordCamp Utrecht 2018
- WordCamp Europe 2018 (Belgrade)
- WordCamp Nijmegen 2018
- WordCamp Europe 2017 (Paris)
- FOSDEM 2016
