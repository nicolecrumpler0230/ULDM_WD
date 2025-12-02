# ULDM_WD
If dark matter is ultra-light and has certain Standard Model interactions, it can change the mass-radius relation of white dwarf (WD) stars. The coherence length of ultra-light dark matter (ULDM) imparts spatial correlations in deviations from the canonical mass-radius relation, and thus WDs can be used to reconstruct the coherence length, or equivalently the particle mass, of the dark matter field. ULDM_WD is an open-source pipeline created by [Nicole Crumpler](https://orcid.org/0000-0002-8866-4797) to simulate the observability of such spatial correlations accounting for realistic complications like variable hydrogen envelope thickness, dust, binaries, measurement noise, and distance uncertainties in DA WDs. ULDM_WD also includes code to employ a machine learning approach on simulated WD data in order to measure the background ULDM field coherence length.

The code in this repo was used in the paper ["Searching for Ultra-light Dark Matter in Spatial Correlations of White Dwarf Structure"](https://arxiv.org/abs/2510.00271), which has been submitted to ApJ and is available on the arXiv. In our paper, we find that large deviations from the mass-radius relation (∼10% change in radius) are needed to produce an observable signal given realistic noise sources. We apply our spatial correlation measurement routine to the SDSS catalog of 10,207 DA WDs. We detect a positive spatial correlation among WDs at separations corresponding to a coherence length of 300±50 pc, with an average Z-score of 85 for WDs separated by less than this coherence length. We conclude that this signal is due to observational bias. The signal can be explained by an offset between measurements and theory for nearby cool WDs, and the presence of few, low-temperature WDs with noisy measurements at further distances. With future improvements in WD models and measurement techniques, particularly for cool WDs, this method can provide interesting constraints on ULDM models.

The code in this repo is closely related to my [WDparams](https://github.com/nicolecrumpler0230/WDparams) pipeline for measuring the radial velocities, spectroscopic surface gravities and temperatures, and photometric radii and temperatures of all DA white dwarf stars confidently identified in Data Releases (DRs) 1-16 of the Sloan Digital Sky Survey (SDSS) and in SDSS DR 19. WDparams was used to create a large catalog of DA WD characteristics (published in the paper ["A Large Catalog of DA White Dwarf Characteristics Using SDSS and Gaia Observations"](https://iopscience.iop.org/article/10.3847/1538-4357/ade9a9) and as an official [SDSS Value Added Catalog](https://www.sdss.org/dr19/data_access/value-added-catalogs/?vac_id=10008)), and that catalog was used both to detect the temperature dependence of the WD mass-radius relation ([“Detection of the Temperature Dependence of the White Dwarf Mass-Radius Relation with Gravitational Redshifts”](https://iopscience.iop.org/article/10.3847/1538-4357/ad8ddc)) and in this work on ULDM. 

If you wish to use any part of this work, please cite the "Searching for Ultra-light Dark Matter in Spatial Correlations of White Dwarf Structure", “A Large Catalog of DA White Dwarf Characteristics Using SDSS and Gaia Observations”, and the “Detection of the Temperature Dependence of the White Dwarf Mass-Radius Relation with Gravitational Redshifts” papers as well as all relevant references contained within them.

# Pipeline Summary
- 01_SDSS_Data_and_Quality_Cuts.ipynb: This notebook implements data quality cuts on the [SDSS Value Added Catalog](https://www.sdss.org/dr19/data_access/value-added-catalogs/?vac_id=10008)  to get the catalog used for this analysis
- 02a_Effects_of_Binaries.ipynb: This notebook creates a simulated sample of 10,000 binaries and then characterizes the effect of binarity on SDSS and Gaia photometric fits.
- 02b_Effects_of_Binaries.ipynb: This notebook characterizes the effect of binarity on SDSS spectroscopic fits.
- 03_Effect_of_Dust.ipynb: This notebook investigates whether dust clouds have a characteristic size that could create an ULDM-like effect in a Moran's I curve and then re-fits all photometry with no extinction corrections to characterize the impact of adding dust as a source of noise.
- 04_Simulation.ipynb: This notebook simulates how various noise sources impact our ability to detect the signal of ULDM. We simulate the effect of ULDM on WDs on simple square ULDM grid and we add in the effects of measurement noise (both in physical parameters and in distance to WD), binary contamination, and thin H envelope contamination.
     - Simulation steps:
         - Draw a truth sample of WDs
         - Add in thin H envelope WD contamination
         - Add in the effect of the ULDM field on the radius
         - Add in the effect of dust
         - Add in binary contamination
         - Add in measured parameter uncertainty
         - Add in distance uncertainty
         - Compute the measured mass given the measured radius and surface gravity with the effects of noise and ULDM
         - Compute the spatial correlation (Moran's I) and the significance of that correlation (Z-score)
     - We then run many of these simulations on a super computing cluster, here are the different types of simulations we run:
         - 0: no noise sources with dark matter
         - each 1 noise source turned on with dark matter
              - 1: just thin H
              - 2: just dust with effect_strength=1
              - 3: just dust with effect_strength=10
              - 4: just binaries where remove likely binaries
              - 5: just measurement noise with improvement_factor=1
              - 6: just measurement noise with improvement_factor=10
              - 7: just distance noise
        - 8: all noise sources turned on for median measurement noise (improvement_factor=1),full dust strength (effect_strength=1), remove likely binaries, with dark matter
        - 9: all noise sources turned on where reduce measurement noise by order of magnitude (improvement_factor=10), remove likely binaries, with dark matter
        - 10: all noise sources turned on with improvement_factor=1, where reduce dust effect by order of magnitude (effect_strength=10), remove likely binaries, with dark matter
        - 11: all noise sources turned on where reduce measurement noise and dust strength by order of magnitude (improvement_factor=10, effect_strength=10), remove likely binaries, with dark matter
    - In the following notebooks (05_##_NAME.ipynb) we analyze the outputs of these simulations run on a super computing cluster
- 05_00_No_Noise.ipynb: This notebook trains a machine learning method to reconstruct the correlation length in the simulation with no added noise sources (simulation=0).
- 05_01_Thin_H.ipynb: This notebook trains a machine learning method to reconstruct the correlation length in the simulation with thin H envelope contamination added (simulation=1).
- 05_02_Dust.ipynb: This notebook trains a machine learning method to reconstruct the correlation length in the simulation with dust effected added (simulation=2).
- 05_03_Dust_Effect_Strength_10.ipynb: This notebook trains a machine learning method to reconstruct the correlation length in the simulation with dust effected added but reduced in strength by order of magnitude (effect_strength=10) (simulation=3).
- 05_04_Binaries.ipynb: This notebook trains a machine learning method to reconstruct the correlation length in the simulation with with binary contamination added and where try to remove binaries with a simple cut (simulation=4).
- 05_05_Measurement_Noise_IF1.ipynb: This notebook trains a machine learning method to reconstruct the correlation length in the simulation with measurement noise added, but no improvement in measurement capabilities (IP=1) (simulation=5).
- 05_06_Measurement_Noise_IF10.ipynb: This notebook trains a machine learning method to reconstruct the correlation length in the simulation with measurement noise added, and with an order of magnitue improvement in measurement capabilities (IP=10) (simulation=6).
- 05_07_Distance_Noise.ipynb: This notebook trains a machine learning method to reconstruct the correlation length in the simulation with distance noise added (simulation=7).
- 05_08_All_Noise_Sources.ipynb: This notebook trains a machine learning method to reconstruct the correlation length in the simulation with all noise sources added (simulation=8).
- 05_09_All_Noise_Sources_IF10.ipynb: This notebook trains a machine learning method to reconstruct the correlation length in the simulation with all noise sources added and with reducing measurement noise by order of magnitude (improvement_factor=10)(simulation=9). 
- 05_10_All_Noise_Sources_ES10.ipynb: This notebook trains a machine learning method to reconstruct the correlation length in the simulation with all noise sources added and with reducing dust effect by order of magnitude (effect_strength=10) (simulation=10).
- 05_11_All_Noise_Sources_IF10_ES10.ipynb: This notebook trains a machine learning method to reconstruct the correlation length in the simulation with all noise sources added and with reducing measurement noise and dust strength by order of magnitude (improvement_factor=10, effect_strength=10) (simulation=11).
- 05_Plot.ipynb: This notebook combines the final heatmap plots from previous 05 notebooks into one final figure for the paper.
- 06_Measure_From_My_Data.ipynb: This notebook measures the Moran's I and Z-Score curves as a function of distance for the real data from the SDSS VAC.
- 07_Recreate_Data_Signal_No_ULDM.ipynb: This notebook simulates how to produce an ULDM-like signal by implementing distance-dependent biases and uncertainties.

