---
title: Reanalysis vs. Observation
date: 2019-04-24 11:08:35
tags: [atmospheric sciences, meteorological forcing]
---

As a hydrological modeler, meteorological forcing is always crucial and challenging. Although I've heard re-analysis for a quite long time, I've never really tried to understand it till now. All I knew was that reanalysis is combining observation and model to produce gridded, 3-dimensional atmospheric data.

I found a BAMS essay by Wendy S. Parker (September 2016): *REANALYSES AND OBSERVATIONS - What's the Difference?*.  My idea of reanalysis wasn't too far, yet I was surprised that the re-analysis is not too different from observation except that we have less understanding of accuracy and uncertainty in re-analysis than observation.

Reanalysis is a product of gridded comprehensive snapshots of conditions at a regular interval over long time periods by data assimilation. Data assimilation is a process to use information to estimate as accurately as possible the state of a system (Talagrand 1997) - combining various observations, including ground-based station ship, airplanes, satellite ,radar, etc. and the initial guess from numerical weather prediction (NWP) models. The first guess of NWP models is from the previous observation along with theory-based calculation.

Then, people debated that re-analysis differs significantly from observations:
1. Reanalysis results are obtained by inference, while observation are not.
2. Reanalysis relies on forecasts, while observation does not.
3. Reanalysis involves solving an ill-posed inverse problem, while observation does not.
4. The accuracy of reanalysis results is less well understood than that of observations.

After thoroughly considering the claims one by one, Wendy (2016) concluded:
1. Both observations and reanalyses involve inference - either derived measurements or indirect observations. So it’s not different between both in this perspective.
2. They are different in this case but not significant. The only thing matters is whether results are sufficiently accurate.
3. They are different sometimes but not necessarily significant, it just depends if the solution is sufficiently accurate.
4. The lack of understanding in the accuracy of reanalysis has significant differences in some cases. It's harder to judge the usage of reanalyses in those cases.

That said, I should feel secure while using reanalysis as my meteorological forcing besides the ground-based observation if I don't need to consider the uncertainties. Yet most of reanalysis data has a very coarse spatial-resolution and okay temporal-resolution, I wonder if there any way to get higher resolution reanalysis?

Also, I'm not sure why we don't use the validation or forecasting result to introduce the uncertainty of reanalysis? Do you know?