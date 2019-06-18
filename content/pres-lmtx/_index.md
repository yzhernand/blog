+++
outputs = ["Reveal"]
baseName = "reveal"
title = "Presentation"
date = 2019-06-17T18:16:15-04:00
draft = false
+++

<style type="text/css" media="screen">
    #vntrseeklogop {
        margin-bottom: 3em; 
    }

    #vntrseeklogo {
        background: #268bd2;
        color: #FFFFFF;
        font-size: 3em;
        font-family: "Montserrat", sans-serif;
        font-weight: 600;
        padding: 10px;
    }

    .reveal .spacer {
        margin-bottom: 1em; 
    }

    #acknowledgements {
        text-align: left;
        font-size: 60%;
        column-count: 2;
        -moz-column-count: 2; /* Firefox */
        -webkit-column-count: 2; /* Safari and Chrome */
    }
    .twocolumn {
        text-align: left;
        font-size: 60%;
        column-count: 2;
        -moz-column-count: 2; /* Firefox */
        -webkit-column-count: 2; /* Safari and Chrome */
    }
</style>

<section data-noprocess style="text-align: left">
    <h1 class="stretch" style="font-size: 1.55em; line-height: 1.1; color: white;">A Study of the Variability of<br> <span style="color: #FF8181;">Minisatellite Tandem Repeat Loci</span> </br>in the Human Genome Based on<br>
        <span style="color: #FF8181;">High-Throughput Sequencing Data</span>
    </h1>
    <p style="text-align: right;">Yozen A Hernandez</p>
</section>
<section data-noprocess>
    <h2 style="text-align: left;">Genetic variation</h2>
    <h1 style="font-size: 1.5em; margin-bottom: 1em;">~5 million sites</h1>
    <p style="font-size: 0.8em;">
        on average differ between reference and any individual
    </p>
    <p style="font-size: 1em; margin-top: 1em;">
        <br>
        ~20 Mbp (~0.6%)
    </p>
    <p style="text-align: right; font-size: 0.5em;">
    (The 1000 Genomes Project Consortium, 2015)
    </p>
</section>
<section data-noprocess>
        <h2 style="text-align: left">Genetic polymorphism</h2>
        <table style="text-align: center">
        <tr>
            <td style="width: 50%;">
                SNPs
                </br>
                <img style="height : 100%; width: auto; background: #FFFFFF" alt="SNP" src="figures/200px-Dna-SNP.svg.png">
                </br>
                <small style="font-size: 0.5em"><strong>Source:</strong> Wikipedia</small>
            </td>
            <td style="width: 50%;">
                CNVs
                </br>
                <img style="height : 100%; width: auto; background: #FFFFFF" alt="CNV" src="figures/157px-Gene-duplication.png">
                </br>
                <small style="font-size: 0.5em"><strong>Source:</strong> Wikipedia</small>
            </td>
        </tr>
        <tr>
            <td colspan=2 style="width: 100%;">
                Indels</br>
                <p>
                <span style="font-size: 1em; font-family: monospace; color: white;">
                GCTGGTCATA
                <span style="color: #FF8181;">ACT</span>
                AGTTACT
                </span>
                </p>
                <p>
                <span style="font-size: 1em; font-family: monospace; color: white;">
                GCTGGTCATA
                <span style="color: #FF8181;">---</span>
                AGTTACT
                </span>
                </p>
            </td>
        </tr>
        </table>
</section>
<section data-noprocess>
    <h2 style="font-size: 1.5em;">~50-69%</h2>
    <p style="font-size: 0.8em;">
        of the human genome consists of repetitive DNA
    </p>
    <p style="text-align: right; font-size: 0.5em;">
        (Treangen, 2011)
    </p>
</section>

---

## Tandem Repeats

<object id="tr_example" data="figures/tr_example.svg" type="image/svg+xml" style="width: 100%; position: absolute; left: 50%; margin-left: -50%;"></object>

---

## VNTRs

<object id="vntr_example" data="figures/vntr_example.svg" type="image/svg+xml" style="width: 100%; position: absolute; left: 50%; margin-left: -50%;"></object>

---

<section data-noprocess style="text-align: left;">
    <table style="font-size: 0.8em; float: left; width: 60%">
    <tr>
        <td>
            <h2>Diseases</h2>
            <ul>
                <li>Fragile X syndrome</li>
                <li>Huntington's disease</li>
                <li>Friedrich's ataxia</li>
            </ul>
        </td>
        <td>
            <h2>Genetics</h2>
            <ul>
                <li>Chromatin structure</li>
                <li>Transcriptional regulation/variability</li>
            </ul>
        </td>
    </tr>
    <tr>
        <td>
            <h2 style="margin-top: 10%">Proteomics</h2>
            <ul>
                <li>Protein structure</li>
                <li>Binding affinity</li>
            </ul>
        </td>
        <td>
            <h2 style="margin-top: 10%">Testing</h2>
            <ul>
                <li>DNA fingerprinting</li>
                <li>Microorganism typing (Multiple<br>Loci VNTR Analysis, or MLVA)</li>
            </ul>
        </td>
    </tr>
    </table>
    <div style="float: right; height: auto; width: 20%; margin-top: 5%">
        <img src="figures/fragilex.gif" alt="Fragile X, Scienceblogs.com" style="width: 100%;">
    </div>
</section>

---

{{% markdown %}}

## TRs

![tr_example_real](figures/182268103.png) <!-- .element: style="height : auto; width: 70%;" -->

{{% /markdown %}}

---

{{% markdown %}}

## VNTRs

![vntr_example_real](figures/182268103_vntr.png) <!-- .element: style="height : auto; width: 30%;" -->

{{% /markdown %}}

---

<section data-transition="fade" >
    <h2 class="fragment fade-out" style="text-align: center;">Microsatellites/STRs/SSRs (&lt;7 bp)</h2>
    <h2 style="text-align: center; margin-top: 2em;">Minisatellites (≥7 bp)</h2>
</section>
<section style="text-align: left">
    <h3>Minisatellite VNTR loci with known disease associations</h3>
    <table cellspacing="10" border="0" style="font-size: 0.7em; border: none;">
    <thead style="font-weight: 700;">
        <tr>
            <th>
                Location
            </th>
            <th style="padding-left: 10px;">
                Pattern length (bp)
            </th>
            <th style="padding-left: 10px;">
                Known Alleles
            </th>
            <th style="padding-left: 10px;">
                Association
            </th>
        </tr>
    </thead>
    <tr>
        <td>
            DRD4, 3rd exon
        </td>
        <td style="padding-left: 10px;">
            48
        </td>
        <td style="padding-left: 10px;">
            2-11 copies. 2, 4, &amp; 7 most common.
        </td>
        <td style="padding-left: 10px;">
            ADHD, clozapine metabolism
        </td>
    </tr>
    <tr>
        <td>
            SLC6A3 (DAT), 3' UTR
        </td>
        <td style="padding-left: 10px;">
            40
        </td style="padding-left: 10px;">
        <td>
            3-11
        </td>
        <td style="padding-left: 10px;">
            Idiopathic epilepsy, ADHD, alcohol/cocaine dependence, susceptibility to PD, protection against nicotine dependence
        </td>
    </tr>
    <tr>
        <td>
            MAOA, promoter
        </td>
        <td style="padding-left: 10px;">
            30
        </td style="padding-left: 10px;">
        <td>
            2, 3, 3.5, 4, or 5. 2 is rare.
        </td>
        <td style="padding-left: 10px;">
            Behavioral disorders. Rare 2 copy repeat associated with violent behavior.
        </td>
    </tr>
    </table>

    <p style="text-align: right; font-size: 0.5em;">(Brookes, 2013)</p>

</section>
<section data-noprocess>
    <div id="vntrseeklogop" style="width: 60%; text-align: center; margin: auto">
        <p id="vntrseeklogo">VNTRseek</p>
        <p style="line-height: 1.5em; font-size: 0.6em; margin-top: 1em;">(Gelfand, 2014) doi: 10.1093/nar/gku642 </p>
    </div>
    <p style="text-align: center; margin-top: 2em;"><a href="https://github.com/yzhernand/vntrseek">github.com/yzhernand/vntrseek</a></p>
</section>
<section data-noprocess style="text-align: center;">
    <h2 style="color: white;">WGS Data + Reference TR set</h2>
    <h2 style="color: white;">=  individual <span style="color: #FA8181;">VNTR </span> profile</h2>
</section>
<section data-noprocess>
    <section data-noprocess>
        <img id="tr_mapping" src="figures/tr_mapping.png" style="background: white; width: 100%; height: auto; position: absolute; left: 50%; margin-left: -50%; border: none;"></img>
    </section>
</section>

---

{{% markdown %}}

## Homozygous VNTR genotype

-1/-1

![vntr_example_real_homozygous](figures/vntr_example_real_homozygous.png) <!-- .element: style="height : auto; width: 50%;" -->

{{% /markdown %}}

---

{{% markdown %}}

## Heterozygous VNTR genotype

0/+1

![vntr_example_real_heterozygous](figures/vntr_example_real_heterozygous.png) <!-- .element: style="height : auto; width: 90%;" -->

{{% /markdown %}}

---

{{% markdown %}}

## VNTRseek calling performance

![vntr_example_real_homozygous](figures/sim_vntr_call_acc_measures.png) <!-- .element: style="height : auto; width: 90%;" -->

{{% /markdown %}}

---

{{% markdown %}}

## VNTRseek runtime performance

| Step | Improvement |
|:----|----:|
| TR detection | 45.15% |
| Clustering | 66.82% |
| Recording Flanks | 17.24% |
| Recording Mappings | 22.39% |
| VNTR calling | 4.88% |
| Writing output | 31.48% |
| <span style="color: #FF8181; font-weight: 700;">Overall</span> | <span style="color: #FF8181; font-weight: 700;">38.74%</span> |

{{% /markdown %}}

---

<!--                <section data-markdown style="text-align: left;">
    <script type="text/template">
        ![DNA Fingerprinting](figures/dna_fingerprinting_lg.jpg "DNA fingerprinting") .element: style="border: 0px; margin-bottom: 80px; width: 100%; height: auto;"
    </script>
</section> -->
<section data-noprocess style="text-align: center;">
    <h3>Human genome analysis: data breakdown</h3>
    <table style="text-align: left; font-size: 0.50em;">
        <thead>
            <tr>
                <th>Samples</th>
                <th>Project</th>
                <th>Avg Read Length</th>
                <th>Read Coverage</th>
                <th>Notes</th>
            </tr>
        </thead>
        <tbody>
            <tr>
                <td>
                    330
                </td>
                <td>
                    1000 genomes project
                </td>
                <td>
                    100 bp
                </td>
                <td>
                    5&ndash;24x
                </td>
                <td>
                    ~7TB of data
                </td>
            </tr>
            <tr>
                <td>
                    16
                </td>
                <td>
                    Illumina "Platinum Genomes" (CEPH 1463)
                </td>
                <td>
                    100 bp
                </td>
                <td>
                    48&ndash;109x
                </td>
                <td>
                    Large pedigree
                </td>
            </tr>
            <tr>
                <td>
                    3
                </td>
                <td>
                    Yoruban trio (HapMap Y117)
                </td>
                <td>
                    <strong style="color: #E69F00;">250 bp</strong>
                </td>
                <td>
                    76&ndash;77x
                </td>
                <td>
                    PCR-Free
                </td>
            </tr>
            <tr>
                <td>
                    8
                </td>
                <td>
                    WGS500 Public genomes
                </td>
                <td>
                    100 bp
                </td>
                <td>
                    25&ndash;111x
                </td>
                <td>
                    Disease study
                    <br>
                    Two trios
                </td>
            </tr>
            <tr>
                <td>
                    1
                </td>
                <td>
                    CHM1
                </td>
                <td>
                    150 bp
                </td>
                <td>
                    42x
                </td>
                <td>
                    PCR-Free, haploid
                </td>
            </tr>
            <tr>
                <td>
                    1
                </td>
                <td>
                    CHM13
                </td>
                <td>
                    <strong style="color: #E69F00;">250 bp</strong>
                </td>
                <td>
                    137x
                </td>
                <td>
                    PCR-Free, haploid
                </td>
            </tr>
            <tr>
                <td>
                    3
                </td>
                <td>
                    GIAB (Ashkenazi Trio)
                </td>
                <td>
                    <strong style="color: #E69F00;">250 bp</strong>
                </td>
                <td>
                    64&ndash;74x
                </td>
                <td>
                    PCR-Free
                </td>
            </tr>
            <tr>
                <td>
                    3
                </td>
                <td>
                    GIAB (Chinese Trio)
                </td>
                <td>
                    <strong style="color: #E69F00;">148&ndash;250bp</strong>
                </td>
                <td>
                    117&ndash;355x
                </td>
                <td>
                    PCR-Free
                </td>
            </tr>
            <tr>
                <td>
                    1
                </td>
                <td>
                    GIAB (NA12878/HG001)
                </td>
                <td>
                    <strong style="color: #E69F00;">148 bp</strong>
                </td>
                <td>
                    306x
                </td>
                <td>
                    PCR-Free
                </td>
            </tr>
            <tr>
                <td>
                    4
                </td>
                <td>
                    Tumor/Normal
                </td>
                <td>
                    101 bp
                </td>
                <td>
                    41&ndash;72x
                </td>
                <td>
                    2 paired samples (tumor vs normal)
                </td>
            </tr>
        </tbody>
    </table>
</section>
<section data-noprocess>
    <table cellspacing="50" style="font-size: 0.8em; margin-bottom: 10%;"> 
        <thead>
        <tr>
        <th style="text-align:center;"> Reference TRs</th>
        <th style="text-align:center;"> VNTR Calls </th>
        <th style="text-align:center;"> Indistinguishables  </th>
        <th style="text-align:center;"> Multi  </th>
        <th style="text-align:center;"> Remaining  </th>
        </tr>
        </thead>
        <tbody>
        <tr style="vertical-align:baseline;">
        <td style="text-align:center;" > 228,486</td>
        <td style="text-align:center;" >   13,205    </td>
        <td style="text-align:center;" >   3,273    </td>
        <td style="text-align:center;" > 698 </td>
        <td style="text-align:center;" >  <strong>9,234</strong>    </td>
        </tr>
        </tbody>
    </table>
</section>
<section data-markdown style="text-align: center">
    <script type="text/template">
        ![Coverage vs VNTRs found](figures/scatterplot_cov_vs_vntrs_singleton_full.png)<!-- .element: style="width: 85%; height: auto; border: 0px; margin-top: -1.5%" -->
    </script>
</section>
<section data-markdown style="text-align: center">
    <script type="text/template">
        ![Ratio of all singleton VNTRs to TRs across chromosomes](figures/vntr_prop_chr_distribution.png)<!-- .element: style="width: 85%; height: auto; border: 0px; margin-top: -1%" -->
    </script>
</section>
<section data-markdown style="text-align: center">
    <script type="text/template">
        ![Alleles Per VNTR Locus](figures/allele_counts_per_locus_sing.png)<!-- .element: style="width: 85%; height: auto; border: 0px; margin-top: -2%" -->
    </script>
</section>
<section data-markdown style="text-align: center">
    <script type="text/template">
        ![Copies more or less than the reference](figures/copies_more_or_less_with_inset_singletons.png)<!-- .element: style="width: 85%; height: auto; border: 0px; margin-top: -2%" -->
    </script>
</section>
<section data-noprocess>
    <section data-markdown style="text-align: center">
        <script type="text/template">
            ![Excess of copy gain to loss](figures/copy_gain_minus_loss_plots_100-101bp_cnall.png)<!-- .element: style="width: 85%; height: auto; border: 0px; margin-top: -2%" -->
        </script>
    </section>
    <section data-markdown style="text-align: center">
        <script type="text/template">
            ![Excess of copy gain to loss](figures/copy_gain_minus_loss_plots_250bp_cnall.png)<!-- .element: style="width: 85%; height: auto; border: 0px; margin-top: -2%" -->
        </script>
    </section>
</section>
<section data-markdown style="text-align: center">
    <script type="text/template">
        ![Expected VNTR detection](figures/expected_vntr_detection_100-101bp.png)<!-- .element: style="width: 85%; height: auto; border: 0px; margin-top: -2%" -->
    </script>
</section>
<section data-markdown style="text-align: center">
    <script type="text/template">
        ![Coverage vs VNTRs found](figures/scatterplot_cov_vs_trs_singleton_full.png)<!-- .element: style="width: 85%; height: auto; border: 0px; margin-top: -1.5%" -->
    </script>
</section>
<section data-markdown style="text-align: center">
    <script type="text/template">
        ![Number of Samples Calling a Non-reference Allele Per locus](figures/samples_per_vntr_locus_sing.png) <!-- .element: style="width: 85%; height: auto; border: 0px; margin-top: -2%" -->
    </script>
</section>
<section data-markdown style="text-align: center">
    <script type="text/template">
        ![Number of Samples Calling a Non-reference Allele Per locus](figures/tr_vntr_annotation_table_inv.png) <!-- .element: style="width: 90%; height: auto; border: 0px; margin-top: -2%; filter: invert(1);" -->
    </script>
</section>
<!-- <section data-noprocess>
    <table style="font-size: 0.8em"> 
        <thead>
            <tr>
                <th style="text-align:center;"> Set  </th>
                <th style="text-align:center;"> Intergenic  </th>
                <th style="text-align:center;"> Promoter  </th>
                <th style="text-align:center;"> 5’ UTR  </th>
                <th style="text-align:center;"> Coding  </th>
                <th style="text-align:center;"> Splice Site </th>
                <th style="text-align:center;"> Intron  </th>
                <th style="text-align:center;"> 3’ UTR  </th>
            </tr>
        </thead>
        <tbody>
            <tr style="vertical-align:baseline;">
                <td style="text-align:center;" >    VNTRs       </td>
                <td style="text-align:center;" >    3575        </td>
                <td style="text-align:center;" >    719         </td>
                <td style="text-align:center;" >    93          </td>
                <td style="text-align:center;" >    86          </td>
                <td style="text-align:center;" >    98          </td>
                <td style="text-align:center;" >    4446        </td>
                <td style="text-align:center;" >    95          </td>
            </tr>
            <tr style="vertical-align:baseline;">
                <td style="text-align:center;" >    Singletons (all)    </td>
                <td style="text-align:center;" >    74853       </td>
                <td style="text-align:center;" >    10079       </td>
                <td style="text-align:center;" >    1134        </td>
                <td style="text-align:center;" >    2133        </td>
                <td style="text-align:center;" >    1068        </td>
                <td style="text-align:center;" >    90582       </td>
                <td style="text-align:center;" >    2435        </td>
            </tr>
        </tbody>
    </table>
</section> -->
<section data-noprocess>
    <section style="text-align: left;">
        <h2>Genes with in-frame VNTRs (selected)</h2>
        <table id="inframe_vntrs_table" style="font-size: 0.8em">
            <thead>
                <tr>
                    <th> Gene </th>
                    <th> Description </th>
                </tr>
            </thead>
            <tbody>
                <tr>
                   <td> ZNF544 </td>
                   <td> a zinc finger protein, involved in regulation of RNA polymerase II </td>
                </tr>
                <tr>
                   <td> TP53 </td>
                   <td> a tumor supressor protein </td>
                </tr>
                <tr>
                   <td> PCDH15 </td>
                   <td> a calcium-binding protein in which mutations may result in hearing loss and Usher Syndrome Type 1F </td>
                </tr>
            </tbody>
        </table>
    </section>
    <section id="goterm_slide">
        <h2>Top 10 GO Terms</h2>
        <table id="goterm_table" style="font-size: 0.8em">
         <thead>
          <tr>
           <th style="text-align:left;"> GO Term </th>
           <th style="text-align:right;"> Number of Genes </th>
          </tr>
         </thead>
        <tbody>
          <tr>
           <td style="text-align:left;"> protein binding </td>
           <td style="text-align:right;"> 32 </td>
          </tr>
          <tr>
           <td style="text-align:left;"> cytoplasm </td>
           <td style="text-align:right;"> 21 </td>
          </tr>
          <tr>
           <td style="text-align:left;"> nucleus </td>
           <td style="text-align:right;"> 21 </td>
          </tr>
          <tr>
           <td style="text-align:left;"> integral component of membrane </td>
           <td style="text-align:right;"> 18 </td>
          </tr>
          <tr>
           <td style="text-align:left;"> extracellular exosome </td>
           <td style="text-align:right;"> 14 </td>
          </tr>
          <tr>
           <td style="text-align:left;"> plasma membrane </td>
           <td style="text-align:right;"> 13 </td>
          </tr>
          <tr>
           <td style="text-align:left;"> cytosol </td>
           <td style="text-align:right;"> 12 </td>
          </tr>
          <tr>
           <td style="text-align:left;"> nucleoplasm </td>
           <td style="text-align:right;"> 12 </td>
          </tr>
          <tr>
           <td style="text-align:left;"> membrane </td>
           <td style="text-align:right;"> 11 </td>
          </tr>
          <tr>
           <td style="text-align:left;"> positive regulation of transcription from RNA polymerase II promoter </td>
           <td style="text-align:right;"> 11 </td>
          </tr>
        </tbody>
        </table>
    </section>
</section>
<section style="text-align: left;">
    <h2>Mendelian Consistency</h2>
    <table style="font-size: 0.7em"> 
        <thead>
        <tr>
        <th style="text-align:left;"> Trio  </th>
        <th style="text-align:center;"> All heterozygous, All different  </th>
        <th style="text-align:center;"> Inconsistent  </th>
        <th style="text-align:center;"> All heterozygous  </th>
        <th style="text-align:center;"> Inconsistent  </th>
        </tr>
        </thead>
        <tbody>
        <tr style="vertical-align:baseline; line-height: 1.75em">
        <td style="text-align:left;" >    Yoruban      </td>
        <td style="text-align:center;" >    56       </td>
        <td style="text-align:center;" >   1     </td>
        <td style="text-align:center;" >   353     </td>
        <td style="text-align:center;" >   3     </td>
        </tr>
        <tr style="vertical-align:baseline;">
        <td style="text-align:left;" >    WGS Trio 1      </td>
        <td style="text-align:center;" >    0       </td>
        <td style="text-align:center;" >   NA     </td>
        <td style="text-align:center;" >   21     </td>
        <td style="text-align:center;" >   2     </td>
        </tr>
        <tr style="vertical-align:baseline;">
        <td style="text-align:left;" >    WGS Trio 2      </td>
        <td style="text-align:center;" >    0       </td>
        <td style="text-align:center;" >   NA     </td>
        <td style="text-align:center;" >   26     </td>
        <td style="text-align:center;" >   0     </td>
        </tr>
        <tr style="vertical-align:baseline;">
        <td style="text-align:left;" >    CEPH Trios      </td>
        <td style="text-align:center;" >    (0, 3)       </td>
        <td style="text-align:center;" >   0     </td>
        <td style="text-align:center;" >   (35, 61)     </td>
        <td style="text-align:center;" >   0     </td>
        </tr>
        </tbody>
    </table>
</section>
<section data-markdown style="text-align: left; margin-left: 5%;">
    <script type="text/template">
        # Limitations

        * Other indistinguishable classes
        * Novel TRs
        * Detectable range (read length, low copy number)
    </script>
</section>
<section data-markdown style="text-align: center">
    <script type="text/template">
        ![mlZ Observed distributions](figures/mlz_distrs.png) <!-- .element: style="width: 76%; height: auto; border: 0px; margin-top: -2%" -->

        Credit: Marzie Rasekh <!-- .element: style="font-size: 0.8em;" -->
    </script>
</section>
<section data-markdown style="text-align: center">
    <script type="text/template">
        ![mlZ Decision Tree](figures/mlz_dt.png) <!-- .element: style="width: 76%; height: auto; border: 0px; margin-top: -2%" -->

        Credit: Marzie Rasekh <!-- .element: style="font-size: 0.8em;" -->
    </script>
</section>
<section data-markdown style="text-align: center">
    <script type="text/template">
        ![MLZ Improvement](figures/mlz_improvement.png) <!-- .element: style="width: 80%; height: auto; border: 0px; margin-top: -2%" -->

        Credit: Marzie Rasekh <!-- .element: style="font-size: 0.8em;" -->
    </script>
</section>
<section data-markdown style="text-align: left; margin-left: 5%;">
    <script type="text/template">
        # Applications

        * Expectation of VNTR sizes
        * Cell line typing
        * Resource for poorly understood polymorphisms
        * ...
    </script>
</section>
<section data-markdown style="text-align: center">
    <script type="text/template">
        # VNTRdb

        ![Bootstrap logo](figures/bootstrap-solid.svg) <!-- .element: style="float: left; margin-left: 25%; height: auto; width: 10%; border: none; float: left;" -->

        ![JS logo](figures/240px-Unofficial_JavaScript_logo_2.svg.png) <!-- .element: style="width: 10%; height: auto; border: none;" -->

        ![Mojolicious logo](figures/mojo.png) <!-- .element: style="float: left; margin-left: 25%; height: auto; background-color:white; border: none;" -->                     

        ![SQLite logo](figures/sqlite370_banner.gif) <!-- .element: style="margin-top: 7%; height: auto; border: none;" -->
    </script>
</section>

---

# VNTRdb

<p style="text-align: center; margin-top: 2em;"><a href="http://orca.bu.edu/vntrdb">orca.bu.edu/vntrdb</a></p>

---

<section data-markdown style="text-align: left; margin-left: 5%;">
    <script type="text/template">
        # VNTRdb Features

        * Search by samples, populations, support
        * Browse samples and VNTRs in data set
        * Visualize VNTRs and VNTR alleles
        * Graph VNTR frequencies using search criteria
        * Download data
        * REST API
        * ...
    </script>
</section>
<section data-markdown style="text-align: center">
    <script type="text/template">
        ![VNTRDB VNTR List](figures/vntrdb_vntr_list.png)<!-- .element: style="width: 70%; height: auto; border: 0px; margin-top: -1.5%" -->
    </script>
</section>
<section data-markdown style="text-align: center">
    <script type="text/template">
        ![VNTRDB Sample List](figures/vntrdb_sample_list.png)<!-- .element: style="width: 70%; height: auto; border: 0px; margin-top: -1.5%" -->
    </script>
</section>
<section data-markdown style="text-align: center">
    <script type="text/template">
        ![VNTRDB Sample Page](figures/vntrdb_sample_page.png)<!-- .element: style="width: 90%; height: auto; border: 0px; margin-top: -1.5%" -->
    </script>
</section>
<section data-markdown style="text-align: center">
    <script type="text/template">
        ![VNTRDB VNTR Page](figures/vntrdb_vntr_page.png)<!-- .element: style="width: 90%; height: auto; border: 0px; margin-top: -1.5%" -->
    </script>
</section>
<section data-markdown style="text-align: center">
    <script type="text/template">
        ![VNTRDB VNTR Page](figures/vntrdb_allele_mult_align.png)<!-- .element: style="width: 90%; height: auto; border: 0px; margin-top: -1.5%" -->
    </script>
</section>
<section data-markdown id="acknowledgements" >
    <script type="text/template">
        ## Acknowledgements <!-- .element: style="margin-bottom: 40px" -->

        ### Laboratory for Biocomputing and Informatics: <!-- .element: style="font-size: 0.8em; margin-bottom: 5px" -->

        * Gary Benson
        * Marzie Rasekh
        * Joshua Loving (former)
        * Yevgeniy Gelfand (former) <!-- .element: style="margin-bottom: 25px" -->

        ### Thesis advisory committee: <!-- .element: style="font-size: 0.8em; margin-bottom: 5px" -->

        * Rick Myers
        * Weigang Qiu
        * Stefano Monti
        * Joshua Campbell

        ### Bioinformatics Program: <!-- .element: style="font-size: 0.8em; margin-bottom: 2px; margin-top: 50px" -->
        
        * **"`bubify`"**
        * Beth Becker
        * Johanna Vasquez
        * Caroline Lyman
        * Dave King
        * Many others <!-- .element: style="margin-bottom: 18px" -->

        ### Grants:  <!-- .element: style="font-size: 0.8em; margin-bottom: 2px" -->

        * IGERT, NSF

        ![BU](figures/master_logo.png "Boston University") <!-- .element: style="max-height : 19%; max-width: 19%" -->
    </script>
</section>
<section data-noprocess>
    <h1>Questions?</h1>
    <br>
    <br>
    <p>Download VNTRseek:</p>
    <p class="spacer"><a href="http://orca.bu.edu/vntrseek">http://orca.bu.edu/vntrseek</a></p>
    <p>Source: <br><a href="https://github.com/yzhernand/vntrseek">https://github.com/yzhernand/vntrseek</a></p>
    <br>
    <br>
    <p>Demo VNTRDB</p>
    <p class="spacer"><a href="http://orca.bu.edu/vntrdb">http://orca.bu.edu/vntrdb</a></p>
</section>