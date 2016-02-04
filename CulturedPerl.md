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
  
##The bad  
  
Enough of the nice stuff, now let's get to the JAPHs that are truly "Bad to the Bone." Whereas the good ones teach gently, the bad JAPHs twist your brain into a pretzel. You know a JAPH is bad when ten minutes of staring at it produces only a headache.
###Listing 5. Substitution and eval

```
Date:         26 Mar 90 16:20:37 GMT
From:         raymond@sunkist.berkeley.edu (Raymond Chen)
$_='x"Not ";"x\"another \";\'x\\"perl \\";x\\"hacker,\\"\'"';s/x/print/g;eval eval eval;
```
A common technique, which is illustrated here, is to replace one word with another and then evaluate the output (in effect, building Perl code as you go). The example above replaces every "x" with a "print". You should also be aware of the quoting rules in Perl. What Perl sees in that string, after evaluation, is: x"Not ";"x\"another \";'x\"perl \";x\"hacker,\"'" (Put a print() before the s/// command to see this.)

The string begins with a single quote, so it must end with a single quote. If you look ahead for single quotes you'll see that two are escaped (with a back slash), and the third one is the real thing.

Now, run the substitution (put a print() after the s/// command to see the result for yourself): print"Not ";"print\"another \";'print\"perl \";print\"hacker,\"'"

And there's our command now. Why run three eval() commands on it, instead of just one? Look carefully. The second print() is inside a string, and will not be evaluated by the first eval(). The first eval(), however, will return the evaluated first-level string: print"another ";'print"perl ";print"hacker,"'. And it will print "Not". Why is the first part of the string not returned by the first eval()? Because eval() returns only the last thing evaluated. Make the last statement "print eval" instead of "eval eval eval" to see this effect in operation.

What does the second eval do? It evaluates the second, but not the third or fourth, print() statements. If you look you'll see that both of them are inside one single-quoted string. The second eval will return the string with the third and fourth print() statements in it, and drop the one that just printed "another ". So the second eval will return: print"perl ";print"hacker,"

And the third eval will run those two print() statements to complete the JAPH (which, whimsically, prints out "Not another perl hacker,").

As you can see, a bad JAPH can take a while to unravel. Even something as simple as the one we just decoded can turn out to have several levels of complexity.

###Let's examine another JAPH that's bad to the bone:
Listing 6. Abigail's jawbreaker

```
#Abigail
$_ = "\x3C\x3C\x45\x4F\x54"; s/<<EOT/<<EOT/e; print;
Just another Perl Hacker
EOT
```
Listing 6 also appears to be a simple JAPH. Why? The string is right there -- what's the mystery? Well, Abigail's style is to use something you've seen before in a new way. Here, for instance, the s/// operator is given the "e" modifier. That makes it evaluate the right-side expression before doing the substitution. Thus, "<<EOT" is replaced with everything from the next line up to the EOT on a line by itself -- in this case, "Just another Perl Hacker".

The coded $_ string ends up containing "<<EOT" so the substitution done by the s/// operator ends up replacing the string "<<EOT" with the result of evaluating "<<EOT", which is the string "Just another Perl Hacker". The print() statement takes care of printing that string.

Coded strings and seemingly simple substitutions are a mainstay of JAPHs. Substitutions, in particular, can be made in new and surprising ways that you may find useful in your own code.

Here's another of Abigail's devilish mind twisters:
###Listing 7. Prototypes explained by Abigail

```
#Abigail
perl -wle 'sub _ "Just another Perl Hacker"; print prototype \&_'
```
Understanding this JAPH requires a working knowledge of prototypes. Look at the "perldoc -f sub" and "perldoc -f prototype" documentation to understand what's going on. Basically, a new function is created without a body called "_" and with a prototype of "Just another Perl Hacker".

If you look at the actual section on prototypes in the Programming Perl, 3rd Edition (see Resources), you'll see that prototypes can't be strings. Abigail conveniently ignores that fact (since the function will never be used), and then prints out its invalid prototype.

Is this legal? Perhaps, since it doesn't crash the program. Is it insane? Only slightly so. There are many more insane ones floating around, and this one at least demonstrates that prototype legality is not checked when the function is defined. It also demonstrates that we can define a function called "_" --something you shouldn't usually do, since it may conflict with the built-in "_" operator.  
  
##The ugly  
  
When we go past the good and the bad, all we've got left are the ugly JAPHs. These beasts are ugly by definition, meticulously hand-crafted to inspire awe and trips to the medicine cabinet.

Here's one by Kickstart that you should give to your significant other (domestic pets are not included). Note, it's an ugly JAPH for more than one reason -- the four-line limit is thrown right out the window.
###Listing 8. Hearts afire

```
#Kickstart from http://www.perlmonks.com/
#note: a slight valentine variation :)

      $LOVE=               AMOUR.
    true.cards.        ecstacy.crush 
  .hon.promise.de    .votion.partners.
 tender.truelovers. treasure.affection.
devotion.care.woo.baby.ardor.romancing.
enthusiasm.fealty.fondness.turtledoves.
lovers.sentiment.worship.sweetling.pure
attachment.flowers.roses.promise.poem;
 $LOVE=~ s/AMOUR/adore/g; @a=split(//,
  $LOVE); $o.= chr (ord($a[1])+6). chr
   (ord($a[3])+3). $a[16]. $a[5]. chr
    (32). $a[0]. $a[(26+2)]. $a[27].
      $a[5].$a[25]. $a[8].$a[3].chr
        (32).$a[29]. $a[8].$a[3].
          $a[62].chr(32).$a[62].
           $a[2].$a[38].$a[4].
               $a[3].'.';
                 print
                  $o; 
```
Believe it or not, this thing works. But what does it do?

The variable $LOVE is the first key to the mystery. We mangle the script to produce a look at the $LOVE variable, putting it in an executable file so we can debug as we go.

The output says (without the inserted line breaks), $LOVE is: "AMOURtruecardsecstacycrushhonpromisedevotionpartnerstendertrueloverstreasureaffection
devotioncarewoobabyardorromancingenthusiasmfealtyfondnessturtledovesloverssentiment
worshipsweetlingpureattachmentflowersrosespromisepoem",
which tells us that all those bare words were interpreted by Perl as strings.
###Listing 9. Dump the love

```
#!/usr/bin/perl

use Data::Dumper;

      $LOVE=               AMOUR.
    true.cards.        ecstacy.crush 
  .hon.promise.de    .votion.partners.
 tender.truelovers. treasure.affection.
devotion.care.woo.baby.ardor.romancing.
enthusiasm.fealty.fondness.turtledoves.
lovers.sentiment.worship.sweetling.pure
attachment.flowers.roses.promise.poem;

print Dumper $LOVE;

 $LOVE=~ s/AMOUR/adore/g; @a=split(//,
  $LOVE); $o.= chr (ord($a[1])+6). chr
   (ord($a[3])+3). $a[16]. $a[5]. chr
    (32). $a[0]. $a[(26+2)]. $a[27].
      $a[5].$a[25]. $a[8].$a[3].chr
        (32).$a[29]. $a[8].$a[3].
          $a[62].chr(32).$a[62].
           $a[2].$a[38].$a[4].
               $a[3].'.';
                 print
                  $o; 

```
Now, we replace "AMOUR" with "adore," and then split $LOVE into an array of individual characters called @a. The first element in @a is "a," the second is "d," and so on, making up: "adoretruecards
ecstacycrushhonpromisedevotionpartnerstendertrueloverstreasureaffectiondevotion
carewoobabyardorromancingenthusiasmfealtyfondnessturtledovesloverssentimentworship
sweetlingpureattachmentflowersrosespromisepoem"

Finally, we build up the string $o by picking letters from the @a array. Sometimes we have to modify them -- just to make things interesting -- but in the end love triumphs.

The deconstructed script is:
###Listing 10. Love deconstructed

```
#!/usr/bin/perl

$LOVE = "AMOURtruecardsecstacycrushhonpromisedevotionpartners".
        "tendertrueloverstreasureaffectiondevotioncarewoobaby".
        "ardorromancingenthusiasmfealtyfondnessturtledoveslovers".
        "sentimentworshipsweetlingpureattachmentflowersroses".
        "promisepoem";

$LOVE=~ s/AMOUR/adore/g;

@a=split(//, $LOVE);

$o.= chr (ord($a[1])+6). chr (ord($a[3])+3). $a[16]. $a[5] .
#           j                   u              s       t
 chr (32). $a[0]. $a[(26+2)]. $a[27]. $a[5].$a[25]. $a[8].
# space     a          n        o        t    h       e
 $a[3].chr (32).$a[29]. $a[8].$a[3]. $a[62].chr(32).$a[62].
#  r   space      p       e     r      l    space     l
 $a[2].$a[38].$a[4]. $a[3].'.';
#  o     v      e      r

print $o; 
```
Truly, love is a mystery.  
  
##Discovering JAPHs for yourself  
  
We've looked at a variety of fun mini-scripts, and examined a lot of interesting Perl techniques. JAPHs are meant to be a challenge, so any explanations pale next to the joy of discovering their intricacies for yourself. Be forewarned, the majority of JAPHs require advanced Perl knowledge and patience. The challenge lies ahead -- don't be afraid to test yourself against the other JAPHs cited in the Resources section.
  
But take heart, decoding JAPHs does get easier with practice. Simple tricks, like encoding the string in hex or changing the $, variable get easy to spot (but be careful with them nevertheless).
  
My thanks to all the people that have put time and effort into crafting the JAPHs. Every JAPH teaches something new, and is a delight to behold. No other language, to my knowledge, has such a concise obfuscated form (although no one is sure if that is a source of pride or shame). There are obfuscation contests in many other languages, though. If you are interested in learning or participating, see Resources for places to go. C obfuscation contests are particularly noteworthy.
