# About #

An example would be an Org, which has a Collection of 0-* Orgs.


## Key Design Points ##
* Similar to a (1)-(0-x), at least the first 3 lines.
* But because the child type is the same as the parent, we don't want to delete the children when we delete the parent.
  * So use `.WillCacadeOnDelete(false)`
* Because it's Optional to have a parent (a Root would not have a parent)
  * The ParentFK is Optional (Guid?)
  * The relationship has .HasOptiona()



## ToDo ##

* Verify: Appears missing mention of Children Collection...



## Implementation ##

   modelBuilder.Entity<Organisation>()
			.HasOptional(x => t.Parent)
            .WithMany(x.Children)
            .HasForeignKey(x => x.OwnerFK)
			// When you delete this Property (Parent), 
			// you don't delete every child, so:
			.WillCacadeOnDelete(false);


    public class Organisation
    {
		public int Id { get; set; }
		...
		public virtual Guid? OwnerFK {get;set;}
		public virtual Owner Parent {get;set;} 
		...
		public virtual ICollection<Organisation> Children { get; set; }    
		...
    }



## TroubleShooting ##



## Resources ##

* https://code.msdn.microsoft.com/windowsdesktop/Recursive-or-hierarchical-bf43a96e