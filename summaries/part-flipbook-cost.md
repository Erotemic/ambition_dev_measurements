# Part flipbooks against baked sheets: what parts cost

Written by `scripts/measure_part_flipbook_cost.py` from the latest rows of `part_flipbook_cost.jsonl`. Static numbers are a property of the published assets at a commit; runtime numbers belong to a machine (compare only rows that share a `comparable_key`).

## Static census — 2026-10-03T16:22:55-04:00, commit `8cf8cc225fa5` (dirty), renderer `c38ee49a8b15`

- **143 of 210** published sheets have a part flipbook; the rest: actor, bow_arrow, carl_stargan_vfx, creator_lab_props, cut_rope_anvil, cut_rope_piano, cut_rope_rope, flying_spaghetti_monster_boss_sauce, fsm_meatball, generic_action_fx, generic_exotic_fx, generic_explosions, generic_world_fx, george_booul_vfx, glider, hunting_bow, interdimensional_gate_portal, interdimensional_gate_ring, intro_cart, lasersword, lasersword_with_guns, news_board, ninja_shadow_oni_leader_vfx, noether_vfx, oiler_vfx, patent_clerk_vfx, pca_vfx, pirate_admiral_vfx, pirate_heavy_axe, pirate_heavy_v2, player_combat_review, player_extended, player_social_review, player_traversal_review, polygon_charge_shot, portal_gun, portal_gun_blue, portal_gun_orange, projectile_polygon_vfx, robot_archivist, robot_caster, robot_diver, robot_engineer, robot_guardian, robot_medic, robot_miner, robot_runner, robot_slash, sandbag_armored_review, sandbag_full_review, sanic_ring_prop, sanic_spring_red_prop, shrine, super_mary_o_cinder_beacon, super_mary_o_coin, super_mary_o_cosmic_quasar, super_mary_o_flag, super_mary_o_flag_pole_body, super_mary_o_flag_pole_top, super_mary_o_gasoline_tank, super_mary_o_milk_carton, super_mary_o_pipe, super_mary_o_pipe_body, super_mary_o_pipe_top, super_mary_o_spark_blossom, super_mary_o_star_wand, throwing_javelin.
- Full resolution, those 143: sheet pages **262.3 MiB / 532.1 MTexel**, part pages **130.4 MiB / 212.3 MTexel** (**0.497x** the bytes, **0.399x** the texels), plus **46.8 MiB** of draw tables.
- Tier `0_5x` (39 characters with both): sheet 19.8 MiB, parts 19.6 MiB (**0.991x**).
- Tier `0_25x` (39 characters with both): sheet 7.4 MiB, parts 7.1 MiB (**0.95x**).
- Drawn by the game from parts: **72**; measured costlier than their sheet and drawn baked (`realize: baked`): **71** — absurd_general, admiral_grass_hopper, agent_swarm, anne_druid, bear_mauler, boss, burning_flying_shark, colonial_statesman, dark_lord, eve, fascist_enforcer, flying_spaghetti_monster_boss, fsm_noodling, galwah, general_hero, genghis_can, genghis_cant, ghoul_skulker, girdle, goblin_brute_hammer, goblin_cave_dagger, goblin_desert_bow, goblin_forest_spear, goblin_frost_sword, goblin_shaman_staff, hypatia_prime, imperfect_cellular_automaton, jeff_hinter_armored, kernel_guide, le_beast, mallory, mami_marzakhani, mantis_lancer, merchant_prototype, niels_boar, ninja_heavy, ninja_shadow_duelist, ninja_shadow_oni_leader, pirate_admiral, pirate_cutlass_viper, pirate_heavy_broadside_bess, pirate_heavy_iron_mary, pirate_lookout, pirate_navigator, pirate_quartermaster, pirate_raider, player_robot_fable, puppy_slug, puppy_slug_variant2, puppy_slug_velvet, python_goras, ramen_nujan, raptor_stalker, richard_duckling, sanic, smart_house, smirking_behemoth_boss, snakes_on_a_cartesian_plane, stochastic_parrot, stochastic_parrot_v2, super_sanic, sybil, synthetic_friend, tech_bro_disruptor, trudy, vera_ruin, viking_heavy_shieldmaiden, viking_heavy_warrior, viking_shieldmaiden, viking_warrior, willson.
- Draws per frame: median of characters' max **27**, worst **141** (puppy_slug).

| character | road | frames | sheet MiB | parts MiB | bytes x | texels x | parts | draws/frame mean / max |
| --- | --- | ---: | ---: | ---: | ---: | ---: | ---: | ---: |
| noether | parts | 875 | 66.32 | 2.77 | 0.042 | 0.046 | 857 | 33.12 / 35 |
| perfect_cellular_automaton | parts | 913 | 25.70 | 0.38 | 0.015 | 0.074 | 151 | 14.18 / 16 |
| player_robot_v3 | parts | 1888 | 9.09 | 0.52 | 0.058 | 0.058 | 360 | 20.35 / 22 |
| flying_spaghetti_monster_boss | baked | 79 | 6.97 | 7.45 | 1.069 | 1.096 | 999 | 13.52 / 36 |
| pointed_polygon | parts | 920 | 5.55 | 0.21 | 0.038 | 0.029 | 67 | 16.05 / 17 |
| projectile_polygon | parts | 920 | 5.00 | 0.29 | 0.058 | 0.051 | 80 | 15.07 / 16 |
| performer | parts | 985 | 4.68 | 0.42 | 0.089 | 0.095 | 141 | 25.13 / 27 |
| patent_clerk | parts | 875 | 4.62 | 0.33 | 0.071 | 0.084 | 121 | 16.18 / 18 |
| pugnacious_polygon | parts | 920 | 4.54 | 0.23 | 0.05 | 0.037 | 73 | 15.06 / 16 |
| alice | parts | 375 | 4.52 | 4.21 | 0.93 | 0.715 | 762 | 3.76 / 10 |
| director | parts | 920 | 4.23 | 0.16 | 0.037 | 0.028 | 72 | 21.05 / 22 |
| carl_stargan | parts | 953 | 4.12 | 0.55 | 0.133 | 0.095 | 283 | 21.49 / 24 |
| officer | parts | 926 | 3.85 | 0.18 | 0.047 | 0.028 | 83 | 20.07 / 22 |
| player_robot_v2 | parts | 254 | 3.80 | 4.16 | 1.094 | 0.923 | 1202 | 6.91 / 28 |
| medic | parts | 928 | 3.57 | 0.20 | 0.056 | 0.038 | 102 | 22.09 / 23 |
| niels_boar | baked | 290 | 3.33 | 3.72 | 1.118 | 1.231 | 877 | 6.73 / 25 |
| robot | parts | 173 | 3.26 | 3.50 | 1.073 | 0.985 | 1067 | 8.21 / 30 |
| bob | parts | 375 | 3.02 | 2.88 | 0.954 | 0.707 | 1259 | 9.65 / 39 |
| erdish | parts | 163 | 2.80 | 2.94 | 1.052 | 0.795 | 876 | 13.2 / 33 |
| ninja_shadow_oni_leader | baked | 75 | 2.63 | 2.99 | 1.136 | 1.241 | 1632 | 31.99 / 139 |
| vera_ruin | baked | 243 | 2.46 | 2.84 | 1.153 | 1.071 | 1459 | 14.58 / 63 |
| ninja_shadow_duelist | baked | 75 | 2.20 | 2.45 | 1.116 | 1.302 | 1243 | 24.87 / 91 |
| ramen_nujan | baked | 305 | 2.19 | 2.59 | 1.186 | 1.155 | 4265 | 29.66 / 53 |
| girdle | baked | 305 | 2.05 | 2.69 | 1.314 | 1.199 | 3489 | 24.29 / 59 |
| georg_canter | parts | 82 | 1.83 | 1.72 | 0.936 | 0.714 | 393 | 10.22 / 25 |
| davy_hylbert | parts | 307 | 1.82 | 2.12 | 1.164 | 0.927 | 2805 | 20.33 / 56 |
| super_sanic | baked | 181 | 1.80 | 1.63 | 0.902 | 1.251 | 1205 | 19.08 / 65 |
| willson | baked | 52 | 1.80 | 1.94 | 1.08 | 1.105 | 251 | 5.42 / 21 |
| paul_diracula | parts | 307 | 1.72 | 1.72 | 1.004 | 0.815 | 1796 | 20.36 / 50 |
| trex_enemy | parts | 60 | 1.59 | 1.68 | 1.058 | 0.679 | 1284 | 28.9 / 63 |
| ai_slop | parts | 44 | 1.55 | 1.58 | 1.018 | 0.915 | 42 | 1.0 / 1 |
| pipi_tau | parts | 307 | 1.53 | 1.58 | 1.031 | 0.836 | 2114 | 20.21 / 35 |
| anne_druid | baked | 220 | 1.42 | 1.80 | 1.273 | 1.18 | 2483 | 39.2 / 76 |
| jeff_hinter_armored | baked | 136 | 1.42 | 1.71 | 1.209 | 1.352 | 2303 | 32.35 / 73 |
| richard_duckling | baked | 182 | 1.33 | 1.23 | 0.929 | 0.807 | 976 | 18.05 / 73 |
| sanic | baked | 181 | 1.33 | 1.26 | 0.947 | 1.043 | 1407 | 16.07 / 49 |
| hypatia_prime | baked | 176 | 1.28 | 1.40 | 1.092 | 1.219 | 915 | 10.45 / 34 |
| goblin | parts | 74 | 1.26 | 1.30 | 1.029 | 0.994 | 380 | 5.78 / 33 |
| general_hero | baked | 67 | 1.23 | 1.30 | 1.051 | 1.027 | 357 | 6.72 / 14 |
| joseph_furrier | parts | 286 | 1.22 | 1.26 | 1.032 | 0.727 | 1065 | 9.22 / 33 |
| raid_enforcer | parts | 61 | 1.21 | 1.34 | 1.107 | 0.932 | 757 | 16.7 / 28 |
| fascist_enforcer | baked | 61 | 1.21 | 1.35 | 1.117 | 1.031 | 720 | 15.34 / 28 |
| le_beast | baked | 166 | 1.19 | 1.16 | 0.971 | 0.986 | 1372 | 36.22 / 88 |
| mami_marzakhani | baked | 198 | 1.16 | 1.36 | 1.171 | 1.082 | 981 | 13.64 / 67 |
| tech_bro_disruptor | baked | 61 | 1.11 | 1.18 | 1.062 | 1.068 | 214 | 6.13 / 12 |
| pulse_voyager_captain | parts | 59 | 1.08 | 1.15 | 1.068 | 0.89 | 314 | 8.83 / 21 |
| oiler | parts | 81 | 1.08 | 0.09 | 0.083 | 0.077 | 49 | 16.11 / 17 |
| boss | baked | 50 | 1.07 | 1.21 | 1.128 | 1.191 | 449 | 10.98 / 32 |
| stochastic_parrot_v2 | baked | 103 | 1.07 | 1.56 | 1.454 | 1.64 | 1305 | 13.16 / 41 |
| jeff_hinter | parts | 124 | 1.06 | 1.19 | 1.129 | 0.963 | 1786 | 31.59 / 63 |
| exploding_mite | parts | 51 | 1.05 | 1.12 | 1.067 | 0.988 | 244 | 7.2 / 21 |
| yuclid | parts | 174 | 1.00 | 1.08 | 1.083 | 0.943 | 526 | 10.67 / 20 |
| pirate_heavy_iron_mary | baked | 39 | 0.97 | 1.04 | 1.066 | 1.183 | 550 | 25.97 / 50 |
| dark_lord | baked | 53 | 0.96 | 1.01 | 1.045 | 1.084 | 354 | 8.21 / 23 |
| pirate_heavy_broadside_bess | baked | 39 | 0.96 | 0.97 | 1.007 | 1.096 | 403 | 17.62 / 40 |
| pirate_heavy_salt_annet | parts | 39 | 0.94 | 0.92 | 0.98 | 0.943 | 213 | 8.62 / 28 |
| imperfect_cellular_automaton | baked | 26 | 0.94 | 0.99 | 1.057 | 1.013 | 284 | 11.81 / 26 |
| leib_knives | parts | 83 | 0.90 | 0.95 | 1.051 | 0.954 | 257 | 4.12 / 26 |
| spaghetti_event | parts | 44 | 0.90 | 0.83 | 0.921 | 0.975 | 119 | 4.45 / 14 |
| dividing_mite | parts | 51 | 0.89 | 1.01 | 1.135 | 0.903 | 267 | 8.1 / 16 |
| goblin_cantina_chieftain | parts | 52 | 0.87 | 0.82 | 0.937 | 0.853 | 72 | 1.96 / 8 |
| ninja_heavy | baked | 39 | 0.86 | 1.00 | 1.168 | 1.038 | 612 | 20.87 / 85 |
| paradox_barber | parts | 56 | 0.86 | 0.03 | 0.037 | 0.037 | 11 | 10.98 / 11 |
| absurd_general | baked | 39 | 0.85 | 0.93 | 1.088 | 1.084 | 254 | 9.15 / 17 |
| data_lovelace | parts | 56 | 0.85 | 0.04 | 0.045 | 0.051 | 15 | 14.96 / 15 |
| ranged_skirmisher | parts | 50 | 0.84 | 0.81 | 0.959 | 0.965 | 57 | 1.5 / 5 |
| puppy_slug_variant2 | baked | 44 | 0.83 | 0.87 | 1.048 | 1.162 | 237 | 12.61 / 49 |
| weird_hermit | parts | 47 | 0.82 | 0.92 | 1.128 | 0.406 | 910 | 27.72 / 56 |
| neil_ongras_turfson | parts | 135 | 0.82 | 0.02 | 0.019 | 0.017 | 20 | 16.53 / 18 |
| hunny_horror_boss | parts | 72 | 0.80 | 0.02 | 0.03 | 0.029 | 17 | 13.36 / 16 |
| hand_saint | parts | 44 | 0.78 | 0.72 | 0.92 | 0.967 | 37 | 1.0 / 1 |
| martin_cutta | parts | 162 | 0.75 | 0.81 | 1.069 | 0.983 | 530 | 10.31 / 32 |
| carl_runga | parts | 162 | 0.74 | 0.78 | 1.056 | 0.984 | 447 | 7.02 / 29 |
| companion_dog | parts | 99 | 0.73 | 0.02 | 0.028 | 0.02 | 22 | 19.04 / 20 |
| viking_heavy_shieldmaiden | baked | 46 | 0.69 | 0.89 | 1.28 | 1.349 | 697 | 19.83 / 80 |
| smart_house | baked | 51 | 0.66 | 0.76 | 1.146 | 1.112 | 584 | 17.22 / 34 |
| george_booul | parts | 69 | 0.61 | 0.60 | 0.985 | 0.962 | 102 | 3.19 / 15 |
| viking_shieldmaiden | baked | 52 | 0.60 | 0.66 | 1.099 | 1.039 | 389 | 9.85 / 24 |
| stochastic_parrot | baked | 103 | 0.58 | 0.93 | 1.608 | 1.88 | 1727 | 18.9 / 41 |
| viking_heavy_warrior | baked | 46 | 0.56 | 0.65 | 1.17 | 1.256 | 549 | 16.43 / 27 |
| vault_keeper | parts | 28 | 0.56 | 0.54 | 0.974 | 0.871 | 316 | 15.0 / 25 |
| goblin_shaman_staff | baked | 56 | 0.55 | 0.48 | 0.867 | 0.647 | 906 | 107.09 / 132 |
| busy_beaver | parts | 49 | 0.55 | 0.55 | 0.994 | 0.76 | 98 | 3.82 / 21 |
| architect | parts | 28 | 0.55 | 0.53 | 0.969 | 0.73 | 187 | 7.89 / 16 |
| agent_swarm | baked | 44 | 0.53 | 0.46 | 0.874 | 1.059 | 209 | 11.05 / 30 |
| viking_warrior | baked | 53 | 0.53 | 0.62 | 1.163 | 1.13 | 400 | 10.96 / 30 |
| kernel_guide | baked | 28 | 0.52 | 0.53 | 1.009 | 1.087 | 227 | 10.71 / 19 |
| raptor_stalker | baked | 48 | 0.51 | 0.72 | 1.408 | 1.335 | 998 | 29.1 / 63 |
| synthetic_friend | baked | 44 | 0.51 | 0.50 | 0.982 | 1.086 | 60 | 5.32 / 6 |
| mantis_lancer | baked | 48 | 0.51 | 0.66 | 1.313 | 1.446 | 596 | 14.56 / 41 |
| president_portrait | parts | 45 | 0.50 | 0.52 | 1.037 | 0.975 | 222 | 7.58 / 16 |
| olivia | parts | 28 | 0.49 | 0.52 | 1.054 | 0.997 | 275 | 13.11 / 20 |
| m_leblanc | parts | 32 | 0.49 | 0.05 | 0.094 | 0.102 | 20 | 18.5 / 20 |
| merchant_prototype | baked | 28 | 0.47 | 0.53 | 1.122 | 1.056 | 283 | 13.36 / 23 |
| bear_mauler | baked | 48 | 0.47 | 0.65 | 1.405 | 1.871 | 1042 | 36.0 / 53 |
| colonial_statesman | baked | 45 | 0.46 | 0.51 | 1.11 | 1.052 | 210 | 6.51 / 39 |
| puppy_slug_velvet | baked | 62 | 0.46 | 0.67 | 1.444 | 1.728 | 829 | 14.31 / 29 |
| victor | parts | 28 | 0.46 | 0.47 | 1.017 | 0.874 | 212 | 10.21 / 20 |
| peggy | parts | 28 | 0.45 | 0.46 | 1.011 | 0.77 | 307 | 14.93 / 23 |
| craig | parts | 28 | 0.45 | 0.48 | 1.064 | 0.911 | 341 | 16.39 / 22 |
| judy | parts | 28 | 0.45 | 0.48 | 1.082 | 0.991 | 336 | 16.32 / 32 |
| eve | baked | 28 | 0.45 | 0.55 | 1.225 | 1.507 | 284 | 15.96 / 41 |
| helpful_liar | parts | 44 | 0.44 | 0.43 | 0.968 | 0.77 | 33 | 1.0 / 1 |
| ghoul_skulker | baked | 45 | 0.42 | 0.52 | 1.234 | 1.294 | 1052 | 32.47 / 65 |
| charley_beagle_svg | parts | 65 | 0.41 | 0.02 | 0.046 | 0.042 | 21 | 17.86 / 19 |
| pirate_cutlass_viper | baked | 57 | 0.41 | 0.47 | 1.161 | 1.177 | 442 | 10.54 / 22 |
| goblin_brute_hammer | baked | 50 | 0.40 | 0.38 | 0.946 | 0.43 | 631 | 130.52 / 140 |
| python_goras | baked | 49 | 0.39 | 0.41 | 1.066 | 1.136 | 318 | 11.29 / 19 |
| trudy | baked | 28 | 0.39 | 0.41 | 1.063 | 1.18 | 182 | 7.64 / 20 |
| walter | parts | 28 | 0.38 | 0.40 | 1.038 | 0.98 | 184 | 9.0 / 18 |
| goblin_forest_spear | baked | 50 | 0.38 | 0.33 | 0.867 | 0.546 | 580 | 120.52 / 130 |
| sybil | baked | 28 | 0.38 | 0.41 | 1.096 | 1.025 | 326 | 20.46 / 28 |
| mallory | baked | 30 | 0.37 | 0.41 | 1.12 | 1.045 | 263 | 16.5 / 39 |
| goblin_cave_dagger | baked | 50 | 0.36 | 0.32 | 0.891 | 0.503 | 591 | 123.04 / 132 |
| goblin_frost_sword | baked | 44 | 0.36 | 0.31 | 0.868 | 0.571 | 532 | 125.82 / 130 |
| goblin_desert_bow | baked | 50 | 0.36 | 0.32 | 0.884 | 0.451 | 608 | 124.52 / 134 |
| snakes_on_a_cartesian_plane | baked | 37 | 0.33 | 0.35 | 1.069 | 1.071 | 203 | 7.0 / 23 |
| admiral_grass_hopper | baked | 56 | 0.32 | 0.32 | 1.005 | 1.131 | 438 | 18.39 / 26 |
| pirate_admiral | baked | 38 | 0.30 | 0.37 | 1.26 | 1.076 | 359 | 11.26 / 20 |
| mary_o_v2_fire | parts | 32 | 0.29 | 0.10 | 0.349 | 0.731 | 105 | 7.94 / 11 |
| pirate_raider | baked | 38 | 0.28 | 0.34 | 1.178 | 1.004 | 246 | 8.39 / 18 |
| pirate_navigator | baked | 38 | 0.28 | 0.34 | 1.211 | 1.051 | 265 | 9.0 / 16 |
| snakes_on_a_paper_plane | parts | 37 | 0.28 | 0.27 | 0.948 | 0.904 | 174 | 7.46 / 13 |
| pirate_lookout | baked | 38 | 0.28 | 0.34 | 1.198 | 1.043 | 336 | 11.84 / 23 |
| pirate_quartermaster | baked | 38 | 0.27 | 0.33 | 1.193 | 1.051 | 240 | 8.68 / 25 |
| puppy_slug | baked | 48 | 0.26 | 0.80 | 3.1 | 5.8 | 2402 | 72.77 / 141 |
| fsm_noodling | baked | 26 | 0.25 | 0.38 | 1.514 | 1.732 | 383 | 17.69 / 41 |
| burning_flying_shark | baked | 28 | 0.25 | 0.31 | 1.261 | 1.418 | 474 | 31.68 / 46 |
| genghis_cant | baked | 36 | 0.23 | 0.24 | 1.069 | 1.192 | 121 | 5.33 / 39 |
| marie_curry | parts | 64 | 0.23 | 0.25 | 1.095 | 0.839 | 472 | 21.28 / 43 |
| creator | parts | 26 | 0.22 | 0.21 | 0.96 | 0.86 | 148 | 20.62 / 29 |
| genghis_can | baked | 36 | 0.21 | 0.22 | 1.019 | 1.026 | 200 | 22.17 / 63 |
| mary_o_v2_tall | parts | 30 | 0.20 | 0.07 | 0.349 | 0.391 | 63 | 6.53 / 10 |
| solid_snake | parts | 50 | 0.18 | 0.19 | 1.05 | 0.95 | 187 | 7.22 / 14 |
| galwah | baked | 40 | 0.16 | 0.20 | 1.234 | 1.592 | 373 | 13.82 / 29 |
| trent | parts | 24 | 0.15 | 0.12 | 0.783 | 0.582 | 166 | 27.33 / 31 |
| mary_o_v2 | parts | 25 | 0.14 | 0.05 | 0.394 | 0.621 | 62 | 6.72 / 10 |
| player_robot_fable | baked | 24 | 0.12 | 0.13 | 1.122 | 1.11 | 97 | 4.38 / 28 |
| sandbag | parts | 17 | 0.11 | 0.09 | 0.857 | 0.291 | 127 | 9.65 / 28 |
| smirking_behemoth_boss | baked | 26 | 0.03 | 0.01 | 0.476 | 0.198 | 468 | 36.96 / 124 |
| super_mary_o_fire | parts | 25 | 0.02 | 0.02 | 1.0 | 1.0 | 25 | 1.0 / 1 |
| super_mary_o_tall | parts | 26 | 0.02 | 0.02 | 0.962 | 0.895 | 23 | 1.0 / 1 |
| super_mary_o | parts | 25 | 0.02 | 0.02 | 1.0 | 1.0 | 25 | 1.0 / 1 |

