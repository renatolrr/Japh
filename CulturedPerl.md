#	Cultured Perl: The elegance of JAPH  
  
##JAPH: "Just another Perl hacker  
  
The JAPH format was popularized, to the best of our knowledge, by Randal Schwartz in the early 1990s (several sources agree on this). To this day JAPHs can be found in the wild, produced by tireless artists of the genre such as Abigail on the comp.lang.perl.misc newsgroup.
  
In the following discussion we're going to examine some JAPHs from the canonical list on CPAN (see Resources), which are suitable for beginner to intermediate Perl programmers. I'll give a brief commentary on various techniques here, but the interested reader should refer to Programming Perl, 3rd Edition (see Resources) for further study.
  
To follow along accurately with the examples given here, you must have Perl 5.6.0 installed on your system. Preferably, your system should be a recent (2000 or later) mainstream UNIX installation (Linux, Solaris, BSD). Although the examples may work with earlier versions of Perl and UNIX, and other operating systems, you should consider cases where they fail to function as exercises to solve. Every JAPH is presented in the canonical list format, with a date and author attribution.  
  
##The good  
  
Before we get to the meaty stuff, let's take a look at four fun and easy code pieces written by Randal Schwartz back in the early days of JAPH. Our first example below demonstrates that not all JAPHs need be obscure, and some may even be legible.
###Listing 1. A simple true conditional

```
Date:         18 Jun 90 15:53:11 GMT
From:         merlyn@iwarp.intel.com (Randal Schwartz)
print "Just another Perl hacker," if "you can't think of anything better..."
```
In Listing 1 the if() statement will always evaluate to true since the string is not empty (only the empty string "", the "0" string, the number 0 or its equivalent, or undef evaluate to false). Thus, the print statement will always be evaluated.
###Listing 2. Printf in use

```
Date:         15 Jun 90 22:06:24 GMT
From:         merlyn@iwarp.intel.com (Randal Schwartz)
printf "%s %s %s %s%c", 'Just', 'another', 'Perl', 'hacker', 44
```
Listing 2 is another early specimen, which uses the printf() function to produce the wanted output and demonstrates that Perl can look like C if you want it to.

Schwartz begins to get tricky in Listing 3. Now we give print() a rearranged array, and print it out with spaces between the words ($, is the variable that tells Perl what to put between array elements when printing them out all at once).
###Listing 3. Rearranging an array

```
Date:         5 Jun 90 19:07:58 GMT
From:         merlyn@iwarp.intel.com (Randal Schwartz)
$,=" "; print +("hacker,","Just","Perl","another")[1,3,2,0];
```
The + in front of the array makes print() treat what follows it as a single parameter (an array in this case, because of the parenthesis), instead of taking the parentheses to mean a function call. In other words, we avoid the following: print ('a', 'b')[1]; where print() takes 'a' and 'b' as its first and second parameter to be printed, and then Perl doesn't know what to do with the [1].

Listing 4 is one of the earliest JAPHs on file and is somewhat fancy, utilizing split(), sort(), and grep():
###Listing 4. Sort then grep

```
Date:         6 Feb 90 22:31:17 GMT
From:         merlyn@iwarp.intel.com (Randal Schwartz)
print grep(s/^\d+(.*)/$1 /, sort(split(/ /,"8hacker, 4Perl 1Just 2another")));
```
First, we split the starting string into four elements: "8hacker," "4Perl" "1Just" "2another".
Then we sort -- alphanumerically by default -- producing: "1Just" "2another" "4Perl" "8hacker,".

Note that "10Just" would also have gone before "8hacker" -- this is not a numeric sort.

The sorted list is passed to grep(), which removes any number of starting digits and appends a space to whatever's left. The result is: "Just " "another " "Perl " "hacker, ".

And finally, print() is invoked on that list, printing it literally, element by element.
