# Filtering the show command 

Generally used to show some sort of configuration.

- section.
    `show <something> | section <something>`
    - example: `show running-config | section line vty`
- include.
    `show <something> | include <something>`
    - example: `show ip interface brief | include up`
- exclude.
    `show <something> | exclude <something>`
    - example: `show ip interface brief | exclude unassigned`
- begin.
    `show <something> | begin <something>`
    - example: `show ip route | begin Gateway`