# Exercise using UNIX to search for patterns in human genome

Show how to manually download files from GenBank and Ensembl

Already downloaded the following files:
- hgb38.chr22.GenesNCBI.GFF3		
- hgb38.chr22.RepeatsRepeatMasker.BED
- hgb38.chr22.GenesEnsembl.GFF3		
- hg.b38.chr22.fa

For the following exercises, we will use just the data for Chr22.

Goal of this exercise is to get experience using standard linnux command line tools to manipulate and explore data, focusing on the use of grep, sort, and uniq. Just about all of these explorations could be more efficiently carried out using other linnux tools, for example bioawk and seqtk, but our purposes are to get more comfortable with linnux commands rather than efficiency.

## Exploration 1 

### How many nucleotides are in chromosome 22?

Take a look at the first 50 lines or so. Notice the header line, followed by a bunch of Ns.

What are the N's? Chr22 is an borderline chromosome between an extreme sub-metacentric and an achrocentric chromosome. The first set of N's then are more than likely telomere sequences (possibly centromeric region, but highly doubtful).

Look at the last 50 lines. These are all N's as well. What are these N's?

This is most definitely a teleomeric region.

So the above tells us two things. First we need to exclude the header line before doing any counting.

Second, we may want to discount the N's when counting total number of nucleotides since we are not entirely sure of the sequence or the actual length in those regions.

How do we exclude the header line? There should be only one header since this is only Chr22. How would you verify that is the case?

One solution to excluding the header line:

`
grep -v ">" hg.b38.chr22.fa | less
`

(how to navigate file using less?)

Now that the we are confident we can exclude the header, how about simply counting characters?

`
grep -v ">" hg.b38.chr22.fa | wc -c
`

This gives us a result of 51544447, which is incorrect. How do we know that (look it up in a genome browser maybe)? Why do we see this discrepancy?

(grep is counting line returns as a character)

So how can we easily correct this?

One solution: if we count the number of lines returned by grep after excluding the header, that would give us the number of line feeds in the file, which we can then subtract from the value above to give the total number of nucleotides in the file.

Solution:

`
grep -v ">" hg.b38.chr22.fa | wc -l
`

This should result in a value of 725979, so 51544447 - 725979 = 50818468, the correct total number of nucleotides making up Chr22.

## Exploration 2

### How many A nucleotides are there?

One solution:

`
grep -v ">" hg.b38.chr22.fa | grep A | wc -c
`

This results in a value of 39703200. However, this may be incorrect since once again the problem of line numbers as above. To solve this problem, another solution might be:

`
grep -v ">" hg.b38.chr22.fa | grep -c "A"
`

That seems better. This returns a value of 559200. Is this correct? Not really. Scroll through the data file quickly. Notice there are both upper and lower case letters for the nucleotides. Why is this? (The data has been soft-masked for transposable elements; they all show up as lower case). We need to account for both upper- and lower-case. We need to account for that.

To solve this problem, one solution might be:

`
grep -v ">" hg.b38.chr22.fa | grep -c [Aa]
`

or

`
grep -v ">" hg.b38.chr22.fa | grep -i -c a
`
Either command results in a total of 559200. But wait, that did not change anything? And besides, both estimates seem kind of low considering the total number of nucleotides is 50818468. If the total number should be somewhere around 25% of the total (why?), but not exactly, these estimates are ca. 1%. That is definitely too low. So we suspect something is wrong. Could it be that grep is just counting the number of lines where it finds an A? How can we check this?

One way to check this would be to count the number lines being returned by grep and see if it differs from the grep -c command:

`
grep -v ">" hg.b38.chr22.fa | grep -i a | wc -l
`

Same number right? So that was the problem. Dang. Now we know, -c flag in grep counts lines, not instances. Should have checked the manual.

Let's try a different approach then, one that puts each instance of grep finding the search term into a separate line using -o option, then count the number of lines.

`
grep -v ">" hg.b38.chr22.fa | grep -i -o a | wc -l
`

Notice what we are doing with the above command. We are telling grep to ignore the case of the search by using the -i flag, and telling grep to output as a separate line every instance of finding our search term. We could also tell grep to include the line number as well, but that is unnecessary for our purposes. We then pipe those results to wc to count the number of lines. The end result is 10382214. That seems much better. How can you check to see if the command is behaving as we think?

Now, repeat the above for each of the other three nucleotides.

Results:

- A 10382214
- G 9246186
- C 9160652
- T 10370725

How many Ns are there?

- N 11658686

Does he summation of the fiour nucleotides plus the N's equal our estimate of the total number of nucleotides above? That would be a good check of our numbers.

> Sum 50818468

## Exploration 3

### What percentage of Chr22 is made up of transposable elements? Remember the sequences is soft wrapped.

One solution:

Test solution to make sure it will find all the nucleotides

`grep -v ">" hg.b38.chr22.fa | grep -o [AGCTagctNn] | wc -l
`
> (results 50818468)

Test that solution finds all nucleotides except Ns
`
grep -v ">" hg.b38.chr22.fa | grep -o [AGCTagct] | wc -l
`
> (results 39159777)

Now subtract the two; is it consistent with above's estimate for N's?
> (yup)

Then we are good to go. To estimate the percentage of chromosome transposable elements:
`
grep -v ">" hg.b38.chr22.fa | grep -o [agct] | wc -l
`
>(results 20947476; devide this number by 39159777 to determine percentage coding region, or by 50818468 for entire chromosome)

An alternative solution to the above would be to count the number of each nucleotide separately for both upper and lower-case, then use these numbers to calculate percentages. We can do this using the two commands sort | uniq.

> (discussion of sort and uniq)

One solution demonstrating now how to use sort | uniq to enumerate data:
`
grep -v ">" hg.b38.chr22.fa | grep -o [AGCTagctNn] | sort | uniq -c
`

This is a little slow, but eventually gets what we are looking for.

- 4583339 A
- 4524380 C
- 4521583 G
- 11658691 N
- 4582999 T
- 5798875 a
- 4636272 c
- 4724603 g
- 5787726 t

Are these counts consistent with above?

## Exploration 4

### How many of a particular restriction enzyme cut site?

Generally speaking, for a regular pattern search for say a primer sequence, we could use the following:

`
grep -v ">" hg.b38.chr22.fa | grep "AATTC[CG]AA" | less
`

Very rough, and there are numerous programs that do this in a more elegant and informative way, but it demonstrates a simple unix solution.

Using the same logic for a restriction enzyme cut site, e.g. EcoRI (where can you find sequences for RE cut sites?):

`
grep -v ">" hg.b38.chr22.fa | grep --color "GAATTC\|CTTAAG"
`

*or*

`
grep -v ">" hg.b38.chr22.fa | grep -o -i "GAATTC\|CTTAAG" | wc -l
`
to get all sites and count.


##### How many of these sites are in transposon regions? Non transposon sites?

##### Other restriction enzymes?

##### DI-nucleotide repeats? Tri-nucleotide repeats?

## Exploration 5
### Using a gff file

You have been provided with two GFF files for the Chr22 data. One based on the GenBank gene models (hgb38.chr22.GenesNCBI.GFF3), the other the Ensembl based models (hgb38.chr22.GenesEnsembl.GFF3). We will use the Ensembl here only because it is will be a little more simple demonstration.

### How many unique identifiers are there in the Chr22 region?

First off, take a look at the two GFF files. The format should make sense to you since you already read the information provided earlier on basic file formats (right?). To count the number of unique identifiers, one solution might be first strip out the header lines, then cut out the column that contains the identier information, and finally sort | uniq to come up with a final table. 

`
grep -v ^# hgb38.chr22.GenesEnsembl.GFF3 | cut -f 3 | sort | uniq -c | sort -k1 -nr
`

### How many Ensemble genes entries are there in the Chr22 region?

Using the ensembl based GFF file, it looks like we can find the ensembl gene entries in the identifier labeled "gene_id=". One strategy might be to pull the gene_id information out of the gff file, then sort | uniq it as above:

`
grep -v ^# hgb38.chr22.GenesEnsembl.GFF3 | grep -o "gene_id=\w*" | sort | uniq -c | sort -k 1 -nr
`

That looks like it. Why do we have so many entries for each gene id? I thought there might be only one or a few? To answer this, take a close look at the gff file paying attention to order of the entries in the original file, how they are defined, and their position on the chromosome. 

## Exploration 6
### Exploring a bed file

The file named "hgb38.chr22.RepeatsRepeatMasker.BED" contains the results of an analysis of the Chr22 genome using a program called RepeatMasker, which searches for and annotates transposable elements (actually, all repeats) in the genome. The output from RepeatMasker can be a little overwhelming at first, but one option is to simply output the locations of the various repeats in BED file format so thay can easily be used as a track on a genome browser or can be further studied using an app like bedtools.

Using what we have done above, take a look at the format of the file "hgb38.chr22.RepeatsRepeatMasker.BED" (the format should make sense since you read the material for BED files in the file format section of the class right?), then write a simple set of piped unix commands to make a table tabulating the various repeat elements found in this file.

