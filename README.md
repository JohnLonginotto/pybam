# pybam
#### A simple, 100% python, BAM file reader.

pybam is a fast all-python module than you can copy-paste into your code to read a BAM file. Upon loading a BAM file, pybam will parse the header information, and act as a generator to return alignment/read information sequentially as it walks through the file. If you do not need to use BAM indexes, pybam is probably the fastest and simplest BAM parser out there, particularly if run under PyPy.


# Using pybam
### tl;dr:

        >>> import pybam
        >>> bam_file = pybam.read('./ENCFF001LCU.bam')
        >>> for read in bam_file:
        ...     print read
        ...
        SOLEXA1_0001:4:31:10763:18817#0	16	chr1	3073974	0	36M	*	0	0	CTGTTGAAAAACCCAAAAAAAAAAAAAAAAAAAAAA	#################################?:8	NM:i:2	NH:i:18	CC:Z:chr10	CP:i:15165270
        SOLEXA1_0001:4:47:4094:20053#0	0	chr1	3083481	0	36M	*	0	0	GTGCCCTTTAAGTTGAAAATCTTCATTGTCATCAAC	CCCCCCCCCCCCCCCCCCCCCCCCCCCCCCCCCCCB	NM:i:2	NH:i:21	CC:Z:=	CP:i:98646994
        SOLEXA1_0001:4:41:19157:12644#0	16	chr1	3086596	0	36M	*	0	0	TGCCAGGGACTAGCAAACACAGAAGTGGATGCTGCC	####################################	NM:i:2	NH:i:21	CC:Z:=	CP:i:69028922
        SOLEXA1_0001:4:20:2270:14535#0	16	chr1	3094868	0	36M	*	0	0	AAGAAAAAGGAGAAGAAAAAGAAAAAAAAAAAAAAA	##############################BBBBBB	NM:i:2	NH:i:6	CC:Z:chr3	CP:i:128579678
        SOLEXA1_0001:4:36:17421:14269#0	16	chr1	3094871	0	36M	*	0	0	AAAAAAGAAAAGAAAAAAAAAAAGAAAAAAAAAGAG	####################################	NM:i:2	NH:i:21	CC:Z:chr10	CP:i:5884884
        SOLEXA1_0001:4:31:1795:18037#0	16	chr1	3094893	0	36M	*	0	0	AGAAAAAAAAAGAGAAAAAGAAAAAAAAAAAAAAAA	#################################AA@	NM:i:2	NH:i:32	CC:Z:=	CP:i:25751008
        SOLEXA1_0001:4:12:13547:10630#0	0	chr1	3117546	0	36M	*	0	0	TTTTTTTTTTTTTTTTTTTTTTTTGGGTGGTTACCT	CCCCCCBBBBBBBBBBBBBB################	NM:i:2	NH:i:14	CC:Z:chr10	CP:i:57781144
        SOLEXA1_0001:4:86:6040:10633#0	0	chr1	3117547	1	36M	*	0	0	TTTTTTTTTTTTTTTTTTTTTTTGGGGGTTTAGCTT	CCCCCC@B@@@@B@@@@@@@@@59D@@#########	NM:i:2	NH:i:3	CC:Z:chr14	CP:i:115576875
        SOLEXA1_0001:4:26:13139:15177#0	0	chr1	3117548	3	36M	*	0	0	TTTTTTTTTTTTTTTTTTTTTTGGGTGTTTGGCTTC	CCBAAA<>:>7<<>>7:>>@################	NM:i:2	NH:i:2	CC:Z:chr6	CP:i:148390328
        SOLEXA1_0001:4:2:4997:13484#0	0	chr1	3117549	0	36M	*	0	0	TTTTTTTTTTTTTTTTTTTTTGGGTGGTTGGCTTTT	CCCCCCBBBBBBBBB@@@>@47;'?###########	NM:i:2	NH:i:13	CC:Z:chr10	CP:i:18121217
        ...

### Header Info
Pybam can parse data from a BAM file's header:

        >>>> import pybam
        >>>> bam = pybam.read('../ENCFF001LCU.bam')

        >>>> bam.file_name
        'ENCFF001LCU.bam'

        >>>> bam.file_directory
        '/Users/John/Desktop'

        >>>> bam.file_decompressor
        'pigz'

        >>>> bam.file_chromosomes
        ['chr1', 'chr2', 'chr3', 'chr4', 'chr5', 'chr6', 'chr7', 'chr8', 'chr9', 'chr10', 'chr11', 'chr12', 'chr13', 'chr14', 'chr15', 'chr16', 'chr17', 'chr18', 'chr19', 'chrX', 'chrY']

        >>>> bam.file_chromosome_lengths
        {'chr1': 197195432, 'chr2': 181748087, 'chr3': 159599783, 'chr4': 155630120, 'chr5': 152537259, 'chr6': 149517037, 'chr7': 152524553, 'chr8': 131738871, 'chr9': 124076172, 'chr10': 129993255, 'chr11': 121843856, 'chr12': 121257530, 'chr13': 120284312, 'chr14': 125194864, 'chr15': 103494974, 'chr16': 98319150, 'chr17': 95272651, 'chr18': 90772031, 'chr19': 61342430, 'chrX': 166650296, 'chrY': 15902555}

        >>>> print bam.file_header
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

        >>>> with open('newbam.bam','wb') as outfile:
        ....     outfile.write(bam.file_binary_header)

        >>>> bam.file_bytes_read
        1161

        >>>> bam.file_alignments_read
        0

### Read Info
pybam's read return's a generator-class, meaning that every time it is iterated a new read is avaliable for parsing using special parsing codes:
        >>>> read = next(bam)
        >>>> print read
        SOLEXA1_0001:4:49:11382:21230#0	0	chr1	3000743	0	36M	*	0	0	TTTTTTTTGTTTGTTTGTTTTTTTTTTCTGTTTCTT	####################################	NM:i:2	NH:i:13	CC:Z:chr15	CP:i:75293507

        >>>> print read.sam_seq
        TTTTTTTTGTTTGTTTGTTTTTTTTTTCTGTTTCTT

        >>>> print read.sam_qname
        SOLEXA1_0001:4:49:11382:21230#0

        >>>> print bam.sam_qname          # "read" was just a copy of "bam". They are actually the same thing.
        SOLEXA1_0001:4:49:11382:21230#0

        >>>> for x in range(10):
        ....     _ = next(bam)
        ....     print bam.sam_seq
        ....
        GGTCCAAACACCCAAAAAAAAAAAAAAAAAAAAAAA
        TCAAATCAGCCAAAAAAAAAAAAAAAAAAAAAAAAA
        TGCAAGCAGCCAAAAAAAAAAAAAAAAAAAAAAAAA
        TCCAAACTCCCAAAAAAAAAAAAAAAAAAAAAAAAA
        CAAACAGCCAAAAACAAAAAAAAAAAAAAAAAAAAN
        AAAACAGCCAAAAAAACAAAAAAAAAAAAAAAAAAA
        CAAACAGCCCAAAGAAAAAAAAAAAAAAAAAAAAAA
        AAACAGCCAAAGGAAAAAAAAAAAAAAAAAAAAAAA
        AAACCGCCAAAAAAGAAAAAAAAAAAAAAAAAAAAA
        AAACAGCCCCAAAAAAAAAAAAAAAAAAAAAAAAAA
        
### All Parse Codes:
        File data:
            [ Immediately avalible after opening a file with pybam.read ]
            Prefixed with "file_", these properties tell you something about the BAM file itself:
            file_bytes_read         - A running counter of decompressed BAM bytes read. Note that this is not the same as compressed bytes read, which is not currently calculated. Counter incremented in blocks as the file is read in blocks. Useful for status bars if the number of decompressed bytes is known in advance. Unfortunately, while the BAM format does technically have a mechanism for storing the uncompressed size of the BAM file, it is only reliable for files less than 4.3Gb bytes in size, so not provided.
            file_alignments_read    - A running counter of BAM alignments/"reads" parsed so far. Useful for status bars if the number of reads are known in advance. Unfortunately, in the BAM format, there is no way to know in advance how many alignments there are in the file. It must all be read first.

            file_header             - The ASCII header from the BAM file (the output of "samtools view -H")
            file_binary_header      - The original binary header, i.e. all the bytes up until the first alignment entry ("read"). Useful when making a new BAM file the lazy way by copy/pasting an existing header.
            file_chromosomes        - The chromosome names from the binary header of the BAM file
            file_chromosome_lengths - The chromosome lengths from the binary header of the BAM file

            file_name               - The file name of the BAM file, or '<stdin>' if reading from stdin.
            file_directory          - The directory of the BAM file, or the current working directory if reading from stdin.
            file_decompressor       - The decompression program used (pybam,pigz,gzip,internal)

        BAM/SAM data:
            After at least 1 alignment has been parsed, the sam/bam properties can be used to pull out information from the BAM file one entry at a time.
            Alignments are parsed whenever the pybam.read() object is iterated, i.e. "my_parsed_bam = pybam.read(my_bam)" will not parse any alignments, but as soon as the BAM
            is iterated with either "next(my_parsed_bam)" or "for read in my_parsed_bam:" then the sam/bam properties can be accessed via either my_parsed_bam.X or read.x respectively.
            sam              - Ideally as close to what samtools would print for the current entry if viewed via "samtools view"
            bam              - All the bytes that make up the current entry, still in binary just as they were in the BAM file. Useful when creating a new BAM file of filtered alignments.
            sam_qname        - The QNAME (Fragment ID) of the alignment [first column in a SAM file]
            bam_qname        - The QNAME data directly from the BAM file, which is essentially the same as sam_qname but with a NUL byte on the end.
            sam_flag         - The FLAG number of the alignment [second column in a SAM file]
            bam_flag         - The original bytes before decoding to give the FLAG value.
            sam_refID        - The chormosome ID (not the same as the name!) for the current entry. Chromosome names are stored in the BAM header (file_chromosomes), NOT in the alignments
                               as thus to convert a refID to an actual chromosome name, one needs to do my_parsed_bam.file_chromosomes[read.sam_refID]. But for comparisons, using the refID
                               is much faster that using the actual chromosome name (for example, when reading through a sorted BAM file and looking for where last_refID != read.sam_refID)
                               Note that this value is negative when the alignment is not aligned, and thus one must not perform my_parsed_bam.file_chromosomes[read.sam_refID] without checking
                               that the value is positive first.
            sam_rname        - A method that does actually give the chromosome name (using file_chromosomes[sam_refID]). Will return "*" for negative values. [third column in a SAM file]
            bam_refID        - The original binary bytes before decoding to sam_refID
            sam_pos1         - The 1-based position of the alignment. Note that in SAM format values less than 1 are converted to '0' for "no data" and sam_pos1 will also do 
                               this. [fourth column in a SAM file]
            sam_pos0         - The 0-based position of the alignment. Note that in SAM all positions are 1-based, but in BAM they are stored as 0-based. Unlike sam_pos1, negative
                               values are kept as negative values here, essentially giving you the decoded value as it was stored in the BAM.
            bam_pos          - The original bytes before decoding to sam_pos0.
            sam_mapq         - The Mapping Quality of the current alignment [fifth column in a SAM file]
            bam_mapq         - The original binary for the above before decoding.
            sam_cigar_string - The CIGAR string, as per the SAM format. Allowed values are "MIDNSHP=X". [sixth column in a SAM file]
            sam_cigar_list   - A list of tuples with 2 values per tuple: the number of bases with the CIGAR value, and the CIGAR value. Faster to calculate than sam_cigar_string.
            bam_cigar        - All the bytes needed to make the CIGAR field, as it was in the original BAM file.
            sam_next_refID   - The sam_refID of the alignment's mate (if any). Note that as per sam_refID, this value can be negative and is not the chromosome name.
            sam_rnext        - The chromosome name of the alignment's mate (if any). Value is "*" if unmapped. Note that in a SAM file this value is "=" if it is the same as the
                               sam_rname, however pybam will only do this is the user prints the whole SAM entry with "sam". [seventh column in a SAM file]
            bam_next_refID   - The original binary bytes before decoding to sam_next_refID
            sam_pnext1       - The 1-based position of the alignment's mate. Note that in SAM format values less than 1 are converted to '0' for "no data" and sam_pnext1 will also do 
                               this. [eigth column in a SAM file]
            sam_pnext0       - The 0-based position of the alignment's mate. Note that in SAM all positions are 1-based, but in BAM they are stored as 0-based. Unlike sam_pnext1, negative
                               values are kept as negative values here, essentially giving you the value as it was stored in the BAM.
            bam_pnext        - The original bytes before decoding to sam_pnext0.
            sam_tlen         - The TLEN value. [ninth column in a SAM file]
            bam_tlen         - The original binary used to make the sam_tlen before decoding.
            sam_seq          - The SEQ value (DNA sequence of the alignment). Allowed values are "=ACMGRSVTWYHKDBN". [tenth column in a SAM file]
            bam_seq          - The original bytes used to make sam_seq before decoding.
            sam_qual         - The QUAL value (quality scores per DNA base in SEQ) of the alignment. [eleventh column in a SAM file]
            bam_qual         - The original bytes used to make sam_qual before decoding.
            sam_tags_list    - A list of tuples with 3 values per tuple: a two-letter TAG ID, the type code used to describe the data in the TAG value (see SAM spec. for details), and 
                               the value of the TAG. Note that the BAM format has type codes like "c" for a number in the range -127 to +127, and "C" for a number in the range of 0 to 255.
                               In a SAM file however, all numerical codes appear to just be stored using "i", which is a number in the range −2147483647 to +2147483647. sam_tags_list will
                               therefore return the code used in the BAM file, and not 'i' for all numbers.
            sam_tags_string  - Returns the TAGs in the same format as would be found in a SAM file (with all numbers having a code of 'i'). [twelfth column in a SAM file] 
            bam_tags         - All the bytes needed to make sam_tags_list/string, but in the original binary encoding straight from the BAM file.

        Extra SAM/bam data:
            The BAM format stores data that is not in the SAM format, but may be useful in certain situations. Methods have been provided to access this data:
            sam_bin          + The bin value of the alignment (used for indexing reads). Please refer to section 5.3 of the SAM spec for how this value is calculated.
            bam_bin          + The raw binary of the above before decoding
            sam_block_size   + The number of bytes the current alignment takes up in the BAM file minus the four bytes used to store the block_size value itself. 
                               In other words, sam_block_size +4 == bytes needed to store this alignment
            bam_block_size   + Raw binary of the above before decoding.
            sam_l_read_name  + The length of the QNAME plus 1 because the QNAME is terminated with a NUL byte for some reason.
            bam_l_read_name  + The raw binary of the above before decoding
            sam_l_seq        + The number of bases in the seq. Useful if you just want to know how many bases are in the SEQ but do not need to know what those bases are (which requires decoding)
            bam_l_seq        + The raw binary of the above before decoding
            sam_n_cigar_op   + The number of CIGAR operations in the CIGAR field. Useful if you want to know how many CIGAR operations there are, but do not need to know what they are.
            bam_n_cigar_op   + The raw binary of the above before decoding
            
### Decompression
By far the biggest performance consideration when decoding BAM data is decompression. Python's internal gzip decompressor is very very slow, and as such it is always
better to use the system gzip binary for decompressing instead. Users with pigz installed can perform decompression faster by using multiple threads, as pybam will try to 
use the pigz binary if it exists before the gzip binary. If neither exists, pybam will use Python's internal gzip decompressor.
In recent years, new implimentations of the gzip algorithm have been produced independantly by companies Intel and Cloudfare. Both implimentations are faster than standard gzip,
and in fact the Cloudfare version using just 1 process is often faster than pigz with 4 or more. To use one of these alternative decompressors, before looking for either pigz or
gzip, pybam will look for a system command called "pybam". It is assumed that a user who knows what they are doing can create an alias from pybam to any decompression binary they wish.
If the user provided pybam with a path to a file, this path will be provided to the pybam command as the final argument, i.e. "pybam /path/to/file.bam". The user must make sure that
their pybam alias expects the file path to be the last argument also. Alternatively, if pybam is provided with an open file handle instead of a string/path, compressed BAM data will
be piped to the pybam system process via it's stdin, and decompressed data will be read from it's stdout, as is typical with decompression programs.
For users who only ever use pybam to access their data, this allows one to completely remove gzip compression on a BAM file, and then re-compress the file using any compression
algorithm they wish, such as the much faster LZO algorithm (roughly 8x faster to decompress, and at the highest compression level gives a comparible file size to gzip).
Totally uncompressed BAM data can also be provided, where the decompression program used is simply cat (or read from stdin).

### Dynamic vs Static Parsing
Pybam provides two methods to parse BAM data line-by-line: either statically, or dynamically.
With a dynamic parser, data is parsed as it is needed. For example:

        >>> for read in pybam.read('/path/to/file.bam'):
        ...    print read.sam_qname
        ...    print read.sam_mapq
        ...    print read.sam_seq
        ...    (....etc)

Here, these values are parsed out of the raw BAM file as they are needed. Two calls to a datum, such as sam_seq, would require the data to be parsed twice. Conversely, the less data one wishes to extract, the quicker one can read the BAM file as data that is not needed is not parsed out of its original binary encoding. To parse data using a static parser, simply provide a list/tuple of fields you wish to extract as a second argument to pybam.read, for example:

    >>> for qname,mapq,seq in pybam.read('/path/to/file.bam',['sam_qname','sam_mapq','sam_seq']):
    ...    print qname
    ...    print mapq
    ...    print seq
    
The static parser should, in an ideal world, be faster than the dynamic parser, although some optimizations can still be performed and are planned for the future.  To see the code generated by the static parser, look at the _static_parser_code property of the pybam.read object, for example:

    >>> bam = pybam.read('/path/to/file.bam',['sam_qname','sam_mapq','sam_seq'])
    >>> print bam._static_parser_code
    def parser(self):
        from array import array
        from struct import unpack
        for _ in self._new_entry:
            sam_l_read_name, sam_mapq = unpack("<BB",self.bam[12:14])
            sam_n_cigar_op = unpack("<H",self.bam[16:18])[0]
            sam_l_seq = unpack("<i",self.bam[20:24])[0]
            _end_of_qname = 36 + sam_l_read_name
            _end_of_cigar = _end_of_qname + (4*sam_n_cigar_op)
            _end_of_seq = _end_of_cigar + (-((-sam_l_seq)//2))
            sam_qname        = self.bam[36            : _end_of_qname -1 ]
            sam_seq = ''.join( [ dna_codes[dna >> 4] + dna_codes[dna & 0b1111]   for dna     in array('B', self.bam[_end_of_cigar : _end_of_seq])])[:sam_l_seq]
            yield sam_qname,sam_mapq,sam_seq

The static parser provides a definitive record for how the BAM file was parsed in universal python code. If the code that uses pybam is not kept with the output data it generates (which is considered good practice) then at the very least one should aim to store the code of the static parser along with their output, as it may resolve future questions and is good record keeping.

Note that one can use a combination of static and dynamic parsing if they wish, although this author knows of no good reason to do so.
