## Synopsis ##



## Background ##

The first thing people ask is *"...hold on...have an edm model 
backing CodeFirst in EF...and another Edm model for the APIs...
why not use the same one for both?"*

Make sense to ask to save effort. But there's a reason there are
two. the EF Edm is for modelling *internal* system entities. 

And the second is for modelling *external* API DTOs. 

And much as it would save effort, security trumps everything, 
hence never falling into the lazy trap of using internal entities 
as dto's and exposing the internal structure of storage to external
nefarious users.

Also, you're avoiding brittleness (just by renaming an internal
system entity you could break your API contract...).

## Development Perspective ##


