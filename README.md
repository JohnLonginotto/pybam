# pybam
Very simple, 100% python, BAM file reader.

# Project Status: Alpha

This project has not been code-reviewed, tested, or even run on more than a handful of BAM files - however if we can get it to work reliably, it would make incorporating BAM file reading into python much simpler since there are no external dependencies to install/compile. Furthermore, initial results show it's pretty fast -- although this is most likely because decompression of the BAM files can be done in parallel on pybam (via pigz), utilizing multiple processors. I think it is more likely to help out the pysam/htspython/simplesam projects become faster, rather than be any sort of replacement for them. I'll say that again - this project is not intended to replace pysam, htspython or simplesam! It is simply to provide python programmers a more direct level of access to the BAM data than they previously had before.

# Using pybam

Pybam consists of 1 class and 1 function. The class, bgunzip, will remove the compression from a BAM file (from stdin or a file handle), parse out the header information, and become an iterable that, if used, returns large blocks of pure uncompressed BAM data.

  python
  Python 2.7.10 (default, Jul 14 2015, 19:46:27)
  [GCC 4.2.1 Compatible Apple LLVM 6.0 (clang-600.0.39)] on darwin
  Type "help", "copyright", "credits" or "license" for more information.
  >>> import pybam
  >>> pure_bam_data = pybam.bgunzip('./ENCFF001LCU.bam')
  Using gzip!
  >>> pure_bam_data.bytes_read
  655360

So as you can see, we have already read the first 655360 bytes of the BAM file, which is more than enough to parse out the header information. Note that this is bytes read of the **compressed** BAM file, not decompressed bytes that the class will output. The .bytes_read value will automatically increase as we iterate the BAM file. You can use this if you know the size of the BAM file in advance to make pretty progress bars.

>>> print pure_bam_data.header_text
@SQ	SN:chr1	LN:197195432	AS:mm9	SP:mouse
@SQ	SN:chr2	LN:181748087	AS:mm9	SP:mouse
@SQ	SN:chr3	LN:159599783	AS:mm9	SP:mouse
@SQ	SN:chr4	LN:155630120	AS:mm9	SP:mouse
@SQ	SN:chr5	LN:152537259	AS:mm9	SP:mouse
@SQ	SN:chr6	LN:149517037	AS:mm9	SP:mouse
@SQ	SN:chr7	LN:152524553	AS:mm9	SP:mouse
@SQ	SN:chr8	LN:131738871	AS:mm9	SP:mouse
@SQ	SN:chr9	LN:124076172	AS:mm9	SP:mouse
@SQ	SN:chr10	LN:129993255	AS:mm9	SP:mouse
@SQ	SN:chr11	LN:121843856	AS:mm9	SP:mouse
@SQ	SN:chr12	LN:121257530	AS:mm9	SP:mouse
@SQ	SN:chr13	LN:120284312	AS:mm9	SP:mouse
@SQ	SN:chr14	LN:125194864	AS:mm9	SP:mouse
@SQ	SN:chr15	LN:103494974	AS:mm9	SP:mouse
@SQ	SN:chr16	LN:98319150	AS:mm9	SP:mouse
@SQ	SN:chr17	LN:95272651	AS:mm9	SP:mouse
@SQ	SN:chr18	LN:90772031	AS:mm9	SP:mouse
@SQ	SN:chr19	LN:61342430	AS:mm9	SP:mouse
@SQ	SN:chrX	LN:166650296	AS:mm9	SP:mouse
@SQ	SN:chrY	LN:15902555	AS:mm9	SP:mouse
>>>

This is the ASCII portion of the BAM header. On Page 13 of the SAM spec (https://samtools.github.io/hts-specs/SAMv1.pdf) that would be the third row called 'text'.

>>> print pure_bam_data.chromosomes_from_header
['chr1', 'chr2', 'chr3', 'chr4', 'chr5', 'chr6', 'chr7', 'chr8', 'chr9', 'chr10', 'chr11', 'chr12', 'chr13', 'chr14', 'chr15', 'chr16', 'chr17', 'chr18', 'chr19', 'chrX', 'chrY']

This is literally just a parsing of the above ASCII text, from 'SN:' to 'LN:', to get the chromosome names in the order they appear in the header.

>>> print pure_bam_data.chromosome_names
['chr1', 'chr2', 'chr3', 'chr4', 'chr5', 'chr6', 'chr7', 'chr8', 'chr9', 'chr10', 'chr11', 'chr12', 'chr13', 'chr14', 'chr15', 'chr16', 'chr17', 'chr18', 'chr19', 'chrX', 'chrY']

Here the data for the chromosome names comes from the *binary* portion of the BAM header - called "name" in the SAM spec on row 6. Of course it should match the names and orders of the chromosomes in the ASCII portion of the header, but theres no need for it to, and samtools will do no checking to make sure that is the case. Since pybam originally comes from another (unpublished) project that lets you reorder/remaster BAM file headers on-the-fly, pybam will always check these two are the same, and if they're not, report an error.

>>> print pure_bam_data.chromosome_lengths
[197195432, 181748087, 159599783, 155630120, 152537259, 149517037, 152524553, 131738871, 124076172, 129993255, 121843856, 121257530, 120284312, 125194864, 103494974, 98319150, 95272651, 90772031, 61342430, 166650296, 15902555]

This data is also from the binary portion of the header, called l_ref in the SAM spec on row 7. You could parse it out of the ASCII header to see if it matches up too, but currently that isnt done.

>>> print pure_bam_data.original_binary_header[:3]
BAM

.original_binary_header() is the byte-for-byte BAM header, right up until the first byte of the first read. Above I have only printed the first 3 characters, which for a valid BAM file should always be "BAM". The rest of the header is a mix of ASCII and unprintable binary. You can write this out to disk if you want to make a new valid BAM file header, but with some other read data in the file.

Now we can iterate this class and get back pure, decompressed BAM data, starting from the first read in the file, until the last read in the file:

>>> for counter,data in enumerate(pure_bam_data):
...     print len(data),repr(data[0:20])
...     if counter == 10: break
...
654199 '\x92\x00\x00\x00\x00\x00\x00\x00\xa6\xc9-\x00 \x00\x00\x13\x01\x00\x00\x00'
35536 '\x00\x00\xff\xff\xff\xff\xff\xff\xff\xff\x00\x00\x00\x00SOLEXA'
35536 '\x11\x11\x11\x11\x11\x11\x02\x02\x02\x02\x02\x02\x02\x02\x02\x02\x02\x02\x02\x02'
35536 'AAD\x12""""""""""""""""'
35536 '""""""""""NMC\x01NHC\x01\x81\x00'
35536 '"""""""""""""""""""N'
35536 '\x00\x00\xff\xff\xff\xff\xff\xff\xff\xff\x00\x00\x00\x00SOLEXA'
35536 '\xba\x13\x01\x00\x10\x00$\x00\x00\x00\xff\xff\xff\xff\xff\xff\xff\xff\x00\x00'
35536 '\x1f\x1f!!\x1f\x1f\x14\x0e\x13 \x02\x02\x02\x02\x02\x02\x02\x02NM'
35536 '292:6261#0\x00@\x02\x00\x00\x88\x88\x88\x88\x88'
35536 '\x00@\x02\x00\x00\x88\x88\x88\x88\x88\x88\x88\x82\x11\x11\x11\x11\x11\x11\x11'

As you can see, this data is pretty meaningless unless parsed from BAM to SAM. That will be done by the next part of pybam - the parser(). But there is one final point to mention here for bgunzip. The way BAM files are compressed but still randomly readable is that they are compressed into small chunks called bgzf blocks. The bgzf blocks may have no relation to where reads start and end, so do not rely on bgunzip to give you data which begins at the beginning of a read and ends at the end of a read (except, of course, for the first block which always begins at the beginning of the first read). Expect all the other chunks of decompressed data to contain partial-read data at their start and end. bgunzip has a blocks_at_a_time optional property which you can use to request X bgzf blocks instead of the default of 50 - however this only works if bgunzip does NOT use your local gzip or pigz binary to do the decompression (because then it wont see the blocks at all). If bgunzip IS using gzip or pigz, it will default to reading 35536 bytes from their stdout at a time (which is what we're seeing above). There is no significance in the number 35536, thats just where my fingers fell at the time.

