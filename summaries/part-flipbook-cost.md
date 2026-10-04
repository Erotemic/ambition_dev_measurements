# Part flipbooks against baked sheets: what parts cost

Written by `scripts/measure_part_flipbook_cost.py` from the latest rows of `part_flipbook_cost.jsonl`. Static numbers are a property of the published assets at a commit; runtime numbers belong to a machine (compare only rows that share a `comparable_key`).

## Static census — 2026-10-03T20:24:42-04:00, commit `d9b37a493867` (dirty), renderer `7bd8024160b7`

- **143 of 210** published sheets have a part flipbook; the rest: actor, bow_arrow, carl_stargan_vfx, creator_lab_props, cut_rope_anvil, cut_rope_piano, cut_rope_rope, flying_spaghetti_monster_boss_sauce, fsm_meatball, generic_action_fx, generic_exotic_fx, generic_explosions, generic_world_fx, george_booul_vfx, glider, hunting_bow, interdimensional_gate_portal, interdimensional_gate_ring, intro_cart, lasersword, lasersword_with_guns, news_board, ninja_shadow_oni_leader_vfx, noether_vfx, oiler_vfx, patent_clerk_vfx, pca_vfx, pirate_admiral_vfx, pirate_heavy_axe, pirate_heavy_v2, player_combat_review, player_extended, player_social_review, player_traversal_review, polygon_charge_shot, portal_gun, portal_gun_blue, portal_gun_orange, projectile_polygon_vfx, robot_archivist, robot_caster, robot_diver, robot_engineer, robot_guardian, robot_medic, robot_miner, robot_runner, robot_slash, sandbag_armored_review, sandbag_full_review, sanic_ring_prop, sanic_spring_red_prop, shrine, super_mary_o_cinder_beacon, super_mary_o_coin, super_mary_o_cosmic_quasar, super_mary_o_flag, super_mary_o_flag_pole_body, super_mary_o_flag_pole_top, super_mary_o_gasoline_tank, super_mary_o_milk_carton, super_mary_o_pipe, super_mary_o_pipe_body, super_mary_o_pipe_top, super_mary_o_spark_blossom, super_mary_o_star_wand, throwing_javelin.
- Full resolution, those 143: sheet pages **262.7 MiB / 532.1 MTexel**, part pages **26.2 MiB / 58.0 MTexel** (**0.1x** the bytes, **0.109x** the texels), plus **42.4 MiB** of draw tables.
- Tier `0_5x` (39 characters with both): sheet 20.1 MiB, parts 2.2 MiB (**0.11x**).
- Tier `0_25x` (39 characters with both): sheet 7.5 MiB, parts 0.9 MiB (**0.114x**).
- Drawn by the game from parts: **142**; measured costlier than their sheet and drawn baked (`realize: baked`): **1** — fsm_noodling.
- Draws per frame: median of characters' max **21**, worst **50** (georg_canter).

| character | road | frames | sheet MiB | parts MiB | bytes x | texels x | parts | draws/frame mean / max |
| --- | --- | ---: | ---: | ---: | ---: | ---: | ---: | ---: |
| noether | parts | 875 | 66.32 | 2.77 | 0.042 | 0.046 | 854 | 30.12 / 32 |
| perfect_cellular_automaton | parts | 913 | 25.70 | 0.38 | 0.015 | 0.074 | 151 | 14.18 / 16 |
| player_robot_v3 | parts | 1888 | 9.09 | 0.52 | 0.058 | 0.058 | 360 | 20.35 / 22 |
| flying_spaghetti_monster_boss | parts | 79 | 7.05 | 3.92 | 0.556 | 0.602 | 1116 | 20.87 / 32 |
| pointed_polygon | parts | 920 | 5.55 | 0.21 | 0.038 | 0.029 | 67 | 16.05 / 17 |
| projectile_polygon | parts | 920 | 5.00 | 0.29 | 0.058 | 0.051 | 80 | 15.07 / 16 |
| performer | parts | 985 | 4.68 | 0.42 | 0.089 | 0.095 | 141 | 25.13 / 27 |
| patent_clerk | parts | 875 | 4.62 | 0.33 | 0.071 | 0.084 | 121 | 16.18 / 18 |
| pugnacious_polygon | parts | 920 | 4.54 | 0.23 | 0.05 | 0.037 | 73 | 15.06 / 16 |
| director | parts | 920 | 4.23 | 0.16 | 0.037 | 0.028 | 72 | 21.05 / 22 |
| carl_stargan | parts | 953 | 4.12 | 0.55 | 0.133 | 0.095 | 283 | 21.49 / 24 |
| alice | parts | 375 | 3.95 | 0.50 | 0.126 | 0.111 | 708 | 22.6 / 31 |
| officer | parts | 926 | 3.85 | 0.18 | 0.047 | 0.028 | 83 | 20.07 / 22 |
| player_robot_v2 | parts | 254 | 3.84 | 0.50 | 0.129 | 0.146 | 386 | 16.61 / 28 |
| medic | parts | 928 | 3.57 | 0.20 | 0.056 | 0.038 | 102 | 22.09 / 23 |
| niels_boar | parts | 290 | 3.39 | 0.29 | 0.087 | 0.178 | 305 | 28.77 / 34 |
| robot | parts | 173 | 3.38 | 0.52 | 0.154 | 0.187 | 372 | 16.49 / 30 |
| bob | parts | 375 | 3.04 | 0.42 | 0.138 | 0.116 | 368 | 15.81 / 21 |
| erdish | parts | 163 | 2.86 | 0.20 | 0.07 | 0.119 | 211 | 15.0 / 15 |
| ninja_shadow_oni_leader | parts | 75 | 2.78 | 0.08 | 0.029 | 0.041 | 34 | 23.77 / 26 |
| vera_ruin | parts | 243 | 2.52 | 0.42 | 0.166 | 0.239 | 634 | 24.3 / 42 |
| ninja_shadow_duelist | parts | 75 | 2.33 | 0.05 | 0.021 | 0.03 | 29 | 21.77 / 24 |
| ramen_nujan | parts | 305 | 2.22 | 0.21 | 0.095 | 0.135 | 384 | 28.15 / 33 |
| girdle | parts | 305 | 2.12 | 0.13 | 0.062 | 0.115 | 293 | 27.65 / 28 |
| georg_canter | parts | 82 | 2.07 | 0.33 | 0.16 | 0.168 | 346 | 38.66 / 50 |
| willson | parts | 52 | 2.00 | 0.37 | 0.184 | 0.234 | 222 | 27.04 / 32 |
| davy_hylbert | parts | 307 | 1.62 | 0.26 | 0.159 | 0.159 | 368 | 23.0 / 28 |
| trex_enemy | parts | 60 | 1.59 | 0.15 | 0.092 | 0.073 | 219 | 24.88 / 32 |
| paul_diracula | parts | 307 | 1.57 | 0.36 | 0.228 | 0.232 | 393 | 23.96 / 30 |
| ai_slop | parts | 44 | 1.56 | 0.14 | 0.091 | 0.144 | 72 | 18.2 / 20 |
| pipi_tau | parts | 307 | 1.44 | 0.20 | 0.14 | 0.169 | 340 | 19.06 / 25 |
| jeff_hinter_armored | parts | 136 | 1.44 | 0.31 | 0.217 | 0.402 | 288 | 27.19 / 29 |
| anne_druid | parts | 220 | 1.42 | 0.31 | 0.215 | 0.227 | 166 | 17.42 / 18 |
| hypatia_prime | parts | 176 | 1.40 | 0.14 | 0.101 | 0.168 | 261 | 18.11 / 36 |
| richard_duckling | parts | 182 | 1.32 | 0.21 | 0.159 | 0.192 | 130 | 11.97 / 13 |
| general_hero | parts | 67 | 1.30 | 0.05 | 0.039 | 0.051 | 28 | 15.15 / 16 |
| goblin | parts | 74 | 1.26 | 0.41 | 0.327 | 0.327 | 204 | 17.5 / 22 |
| raid_enforcer | parts | 61 | 1.24 | 0.04 | 0.031 | 0.029 | 27 | 19.36 / 22 |
| fascist_enforcer | parts | 61 | 1.23 | 0.04 | 0.032 | 0.029 | 27 | 19.36 / 22 |
| joseph_furrier | parts | 286 | 1.20 | 0.44 | 0.367 | 0.258 | 400 | 16.54 / 33 |
| le_beast | parts | 166 | 1.19 | 0.29 | 0.247 | 0.188 | 112 | 13.58 / 15 |
| super_sanic | parts | 181 | 1.16 | 0.14 | 0.124 | 0.174 | 292 | 23.65 / 27 |
| mami_marzakhani | parts | 198 | 1.14 | 0.15 | 0.131 | 0.161 | 178 | 16.23 / 18 |
| tech_bro_disruptor | parts | 61 | 1.13 | 0.08 | 0.067 | 0.113 | 70 | 18.18 / 22 |
| pulse_voyager_captain | parts | 59 | 1.10 | 0.04 | 0.038 | 0.066 | 56 | 18.08 / 22 |
| oiler | parts | 81 | 1.08 | 0.09 | 0.083 | 0.077 | 49 | 16.11 / 17 |
| boss | parts | 50 | 1.08 | 0.29 | 0.269 | 0.359 | 66 | 12.7 / 15 |
| jeff_hinter | parts | 124 | 1.06 | 0.23 | 0.215 | 0.27 | 209 | 19.68 / 29 |
| exploding_mite | parts | 51 | 1.05 | 0.05 | 0.048 | 0.101 | 33 | 17.73 / 21 |
| pirate_heavy_iron_mary | parts | 39 | 1.00 | 0.14 | 0.138 | 0.253 | 140 | 19.44 / 24 |
| imperfect_cellular_automaton | parts | 26 | 0.97 | 0.15 | 0.152 | 0.183 | 71 | 25.23 / 26 |
| pirate_heavy_salt_annet | parts | 39 | 0.96 | 0.15 | 0.161 | 0.242 | 141 | 19.44 / 24 |
| dark_lord | parts | 53 | 0.95 | 0.14 | 0.146 | 0.29 | 203 | 22.51 / 37 |
| pirate_heavy_broadside_bess | parts | 39 | 0.93 | 0.14 | 0.152 | 0.251 | 140 | 19.44 / 24 |
| dividing_mite | parts | 51 | 0.93 | 0.04 | 0.047 | 0.112 | 31 | 15.73 / 19 |
| yuclid | parts | 174 | 0.92 | 0.25 | 0.276 | 0.311 | 451 | 19.87 / 30 |
| leib_knives | parts | 83 | 0.92 | 0.11 | 0.118 | 0.2 | 93 | 19.96 / 22 |
| stochastic_parrot_v2 | parts | 103 | 0.90 | 0.39 | 0.429 | 0.565 | 289 | 9.43 / 14 |
| goblin_cantina_chieftain | parts | 52 | 0.90 | 0.05 | 0.054 | 0.052 | 25 | 17.52 / 19 |
| ranged_skirmisher | parts | 50 | 0.88 | 0.05 | 0.054 | 0.065 | 30 | 18.3 / 22 |
| ninja_heavy | parts | 39 | 0.87 | 0.17 | 0.195 | 0.218 | 148 | 24.87 / 33 |
| paradox_barber | parts | 56 | 0.86 | 0.03 | 0.037 | 0.037 | 11 | 10.98 / 11 |
| absurd_general | parts | 39 | 0.85 | 0.04 | 0.042 | 0.033 | 21 | 19.31 / 22 |
| data_lovelace | parts | 56 | 0.85 | 0.04 | 0.045 | 0.051 | 15 | 14.96 / 15 |
| weird_hermit | parts | 47 | 0.82 | 0.11 | 0.13 | 0.089 | 124 | 21.32 / 22 |
| neil_ongras_turfson | parts | 135 | 0.82 | 0.02 | 0.019 | 0.017 | 20 | 16.53 / 18 |
| hand_saint | parts | 44 | 0.81 | 0.06 | 0.068 | 0.089 | 20 | 11.27 / 12 |
| goblin_shaman_staff | parts | 56 | 0.81 | 0.25 | 0.304 | 0.327 | 267 | 18.75 / 23 |
| hunny_horror_boss | parts | 72 | 0.80 | 0.02 | 0.03 | 0.029 | 17 | 13.36 / 16 |
| puppy_slug_variant2 | parts | 44 | 0.76 | 0.14 | 0.184 | 0.242 | 93 | 30.41 / 31 |
| companion_dog | parts | 99 | 0.73 | 0.02 | 0.028 | 0.02 | 22 | 19.04 / 20 |
| viking_heavy_shieldmaiden | parts | 46 | 0.72 | 0.04 | 0.057 | 0.076 | 49 | 18.48 / 19 |
| carl_runga | parts | 162 | 0.71 | 0.22 | 0.305 | 0.278 | 257 | 15.81 / 17 |
| sanic | parts | 181 | 0.71 | 0.13 | 0.183 | 0.171 | 287 | 15.65 / 19 |
| smart_house | parts | 51 | 0.69 | 0.10 | 0.152 | 0.183 | 82 | 18.24 / 22 |
| martin_cutta | parts | 162 | 0.67 | 0.22 | 0.32 | 0.281 | 246 | 15.81 / 17 |
| goblin_brute_hammer | parts | 50 | 0.67 | 0.01 | 0.011 | 0.04 | 27 | 18.3 / 22 |
| synthetic_friend | parts | 44 | 0.64 | 0.05 | 0.071 | 0.139 | 12 | 8.39 / 10 |
| goblin_forest_spear | parts | 50 | 0.63 | 0.01 | 0.01 | 0.054 | 26 | 18.3 / 22 |
| viking_shieldmaiden | parts | 52 | 0.62 | 0.03 | 0.042 | 0.066 | 48 | 20.38 / 21 |
| george_booul | parts | 69 | 0.60 | 0.32 | 0.542 | 0.746 | 128 | 3.8 / 6 |
| vault_keeper | parts | 28 | 0.59 | 0.04 | 0.065 | 0.101 | 23 | 15.0 / 15 |
| goblin_desert_bow | parts | 50 | 0.59 | 0.01 | 0.011 | 0.048 | 27 | 18.3 / 22 |
| viking_heavy_warrior | parts | 46 | 0.58 | 0.06 | 0.102 | 0.214 | 65 | 16.48 / 17 |
| stochastic_parrot | parts | 103 | 0.58 | 0.26 | 0.451 | 0.572 | 297 | 9.61 / 15 |
| goblin_cave_dagger | parts | 50 | 0.56 | 0.01 | 0.012 | 0.05 | 27 | 18.3 / 22 |
| spaghetti_event | parts | 44 | 0.56 | 0.25 | 0.453 | 0.259 | 35 | 5.36 / 7 |
| goblin_frost_sword | parts | 44 | 0.55 | 0.01 | 0.012 | 0.064 | 23 | 17.8 / 19 |
| helpful_liar | parts | 44 | 0.54 | 0.05 | 0.1 | 0.206 | 12 | 1.27 / 2 |
| viking_warrior | parts | 53 | 0.54 | 0.03 | 0.058 | 0.13 | 54 | 20.58 / 22 |
| kernel_guide | parts | 28 | 0.54 | 0.05 | 0.085 | 0.135 | 23 | 15.0 / 15 |
| busy_beaver | parts | 49 | 0.53 | 0.08 | 0.154 | 0.234 | 152 | 22.65 / 24 |
| architect | parts | 28 | 0.53 | 0.04 | 0.075 | 0.075 | 23 | 15.0 / 15 |
| raptor_stalker | parts | 48 | 0.52 | 0.11 | 0.205 | 0.207 | 134 | 28.27 / 29 |
| agent_swarm | parts | 44 | 0.52 | 0.07 | 0.143 | 0.297 | 81 | 11.27 / 12 |
| president_portrait | parts | 45 | 0.51 | 0.07 | 0.127 | 0.242 | 100 | 19.84 / 23 |
| mantis_lancer | parts | 48 | 0.51 | 0.12 | 0.233 | 0.292 | 143 | 21.23 / 22 |
| eve | parts | 28 | 0.51 | 0.15 | 0.284 | 0.669 | 126 | 22.86 / 25 |
| olivia | parts | 28 | 0.51 | 0.04 | 0.086 | 0.131 | 24 | 14.0 / 14 |
| craig | parts | 28 | 0.50 | 0.04 | 0.087 | 0.128 | 23 | 15.0 / 15 |
| victor | parts | 28 | 0.50 | 0.03 | 0.068 | 0.084 | 23 | 15.0 / 15 |
| merchant_prototype | parts | 28 | 0.50 | 0.04 | 0.08 | 0.119 | 23 | 15.0 / 15 |
| peggy | parts | 28 | 0.50 | 0.04 | 0.073 | 0.093 | 25 | 15.0 / 15 |
| m_leblanc | parts | 32 | 0.49 | 0.05 | 0.094 | 0.102 | 20 | 18.5 / 20 |
| colonial_statesman | parts | 45 | 0.48 | 0.04 | 0.094 | 0.144 | 58 | 19.4 / 21 |
| bear_mauler | parts | 48 | 0.47 | 0.05 | 0.112 | 0.173 | 65 | 19.5 / 21 |
| puppy_slug_velvet | parts | 62 | 0.47 | 0.35 | 0.74 | 0.981 | 282 | 15.52 / 18 |
| judy | parts | 28 | 0.46 | 0.05 | 0.112 | 0.137 | 22 | 15.0 / 15 |
| ghoul_skulker | parts | 45 | 0.43 | 0.05 | 0.125 | 0.166 | 103 | 19.42 / 21 |
| charley_beagle_svg | parts | 65 | 0.41 | 0.02 | 0.046 | 0.042 | 21 | 17.86 / 19 |
| pirate_cutlass_viper | parts | 57 | 0.41 | 0.14 | 0.348 | 0.461 | 133 | 18.14 / 20 |
| sybil | parts | 28 | 0.40 | 0.03 | 0.084 | 0.123 | 25 | 15.0 / 15 |
| walter | parts | 28 | 0.39 | 0.03 | 0.083 | 0.103 | 23 | 15.0 / 15 |
| trudy | parts | 28 | 0.39 | 0.03 | 0.088 | 0.134 | 25 | 15.0 / 15 |
| python_goras | parts | 49 | 0.37 | 0.10 | 0.274 | 0.422 | 183 | 19.37 / 22 |
| mallory | parts | 30 | 0.36 | 0.08 | 0.234 | 0.366 | 110 | 23.0 / 23 |
| snakes_on_a_cartesian_plane | parts | 37 | 0.35 | 0.01 | 0.035 | 0.058 | 19 | 16.76 / 20 |
| admiral_grass_hopper | parts | 56 | 0.34 | 0.05 | 0.147 | 0.202 | 104 | 20.34 / 21 |
| mary_o_v2_fire | parts | 32 | 0.29 | 0.10 | 0.349 | 0.731 | 104 | 7.91 / 11 |
| snakes_on_a_paper_plane | parts | 37 | 0.28 | 0.01 | 0.05 | 0.06 | 19 | 16.76 / 20 |
| burning_flying_shark | parts | 28 | 0.26 | 0.13 | 0.492 | 0.646 | 114 | 11.0 / 11 |
| puppy_slug | parts | 48 | 0.26 | 0.09 | 0.367 | 0.766 | 171 | 25.85 / 26 |
| fsm_noodling | baked | 26 | 0.25 | 0.27 | 1.081 | 1.279 | 198 | 11.04 / 12 |
| creator | parts | 26 | 0.24 | 0.03 | 0.114 | 0.153 | 27 | 12.46 / 13 |
| marie_curry | parts | 64 | 0.24 | 0.02 | 0.078 | 0.085 | 58 | 16.23 / 17 |
| genghis_cant | parts | 36 | 0.23 | 0.04 | 0.172 | 0.316 | 32 | 14.0 / 14 |
| pirate_admiral | parts | 38 | 0.21 | 0.02 | 0.11 | 0.2 | 106 | 22.05 / 23 |
| genghis_can | parts | 36 | 0.21 | 0.04 | 0.189 | 0.339 | 34 | 14.0 / 14 |
| pirate_lookout | parts | 38 | 0.21 | 0.02 | 0.11 | 0.204 | 107 | 22.11 / 24 |
| pirate_navigator | parts | 38 | 0.21 | 0.02 | 0.116 | 0.2 | 104 | 22.0 / 24 |
| mary_o_v2_tall | parts | 30 | 0.20 | 0.07 | 0.349 | 0.391 | 63 | 6.53 / 10 |
| pirate_quartermaster | parts | 38 | 0.20 | 0.02 | 0.113 | 0.204 | 108 | 23.11 / 25 |
| pirate_raider | parts | 38 | 0.20 | 0.02 | 0.117 | 0.2 | 105 | 23.0 / 25 |
| solid_snake | parts | 50 | 0.18 | 0.02 | 0.135 | 0.25 | 128 | 16.56 / 30 |
| galwah | parts | 40 | 0.16 | 0.05 | 0.347 | 0.831 | 99 | 12.72 / 14 |
| trent | parts | 24 | 0.14 | 0.05 | 0.381 | 0.377 | 19 | 5.0 / 5 |
| mary_o_v2 | parts | 25 | 0.14 | 0.05 | 0.394 | 0.621 | 62 | 6.72 / 10 |
| player_robot_fable | parts | 24 | 0.12 | 0.01 | 0.104 | 0.212 | 21 | 14.25 / 16 |
| sandbag | parts | 17 | 0.11 | 0.08 | 0.781 | 0.297 | 47 | 5.12 / 6 |
| smirking_behemoth_boss | parts | 26 | 0.03 | 0.01 | 0.457 | 0.117 | 63 | 7.35 / 21 |
| super_mary_o_fire | parts | 25 | 0.02 | 0.02 | 1.0 | 1.0 | 25 | 1.0 / 1 |
| super_mary_o_tall | parts | 26 | 0.02 | 0.02 | 0.962 | 0.895 | 23 | 1.0 / 1 |
| super_mary_o | parts | 25 | 0.02 | 0.02 | 1.0 | 1.0 | 25 | 1.0 / 1 |

## Room load, parts against baked (`scripts/measure_hall_load_parts_ab.py`; machine-dependent)

Medians of reps 2..N (rep 1 warms the page cache). `last insert` is when the last character image landed, on the app's clock.

| recorded | key | room | arm | char. images | MP | decode ms | last insert s | live decodes | spikes (worst ms) | wall s | RSS MB |
| --- | --- | --- | --- | ---: | ---: | ---: | ---: | ---: | --- | ---: | ---: |
| 2026-10-04T10:10 | `b4458338ee77` | hall_of_characters | parts | 10.0 | 61.8 | 872.0 | 2.3899999999999997 | 0.0 | 11.5 (419.05) | 7.51 | 1715.75 |
| 2026-10-04T10:10 | `b4458338ee77` | hall_of_characters | baked | 93.0 | 526.9 | 33358.5 | 2.3975 | 0.0 | 5.0 (129.89999999999998) | 6.234999999999999 | 1812.5 |
| 2026-10-04T11:45 | `1ba4f82b734a` | hall_of_characters | parts | 10.0 | 61.8 | 934.0 | 2.315 | 0.0 | 11.5 (354.45) | 17.755 | 1691.9 |
| 2026-10-04T11:45 | `1ba4f82b734a` | hall_of_characters | baked | 93.0 | 526.9 | 34204.5 | 2.5965 | 0.0 | 4.5 (217.7) | 15.524999999999999 | 1804.2 |

