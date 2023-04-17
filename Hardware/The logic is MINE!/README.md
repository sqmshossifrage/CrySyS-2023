First download [Mintest](https://www.minetest.net/) and [Mesecons](https://mesecons.net/). Following the instruction for installing [Mod](https://wiki.minetest.net/Installing_Mods) and [World](https://wiki.minetest.net/Worlds). Explore the world and it seems like a huge circuit with input multiple binary levers, and a single 1-bit output lamp. The goal is clear: try to find an input such that the output lights the lamp up. This is a classical [Boolean satisfiability problem](https://en.wikipedia.org/wiki/Boolean_satisfiability_problem) that can be solved with [`Z3`](https://github.com/Z3Prover/z3). 

Then we realize that it's not possible to manually parse the circuit - it has so many wires and gates. A search online indicates that [Google CTF Quals has a similar challenge](https://ctftime.org/task/8795). Moreover, [LiveOverflow](https://www.youtube.com/@LiveOverflow) has a [writeup](https://liveoverflow.com/minetest/) and [video](https://youtu.be/nI8Q1bqT8QU) on that challenge. 

First download LiveOverflow's solution script and run with our fingers crossed. It does not work but luckily we have the same orientation of circuit as in the demo of LiveOverflow. However, we can still follow LiveOverflow's workflow, by first reading the [documentation](https://github.com/minetest/minetest/blob/master/doc/world_format.txt) for `map.sqlite`. We realize that for the new version of the map, the compression algorithm and some data fields have changed. Moreover, we are dealing with a much smaller map so we adjust `H = 33` and `W = 24`. After writing a database parser ourselves, we are able to parse the map. 

We discover some new components presenting in our map: a `nor` gate. This implies we want to add
```python
elif 'mesecons_gates:nor' in id_to_name[asd]:
    GRID[x+i][y+j] = 'R'
```
to the parser. Then we also need to take care of the `nor` gate in the `Z3` SAT solver part. However, note that `nor` is just a composition of first applying `or` and then applying `not`. Thus we need to add something like
```python
elif c == 'R' and d == 'left':
    if 'NOR_{}_{}'.format(x,y+1) not in VISITED:
        VISITED['NOR_{}_{}'.format(x,y+1)] =simplify(Not(Or(trace((x,y+1),'down'), trace((x,y-1),'up'))))
    return VISITED['NOR_{}_{}'.format(x,y+1)]
elif c == 'R' and d == 'down':
    return (x,y+1),'NOR_{}_{}'.format(x,y+1)
elif c == 'R' and d == 'up':
    return (x,y-1),'NOR_{}_{}'.format(x,y-1)  
```

One potential improvement comes to the naming of Boolean variables in `Z3`. When the circuit parser hits a lever, it returns `return Bool('input_'+(str(y).zfill(3)))` so the name of Boolean variables can be easily sorted. To solve the circuit, use 
```python
s = Solver()
s.add(circuit==True)
if s.check() == sat:
    m = s.model()
```
If everything works correctly, the satisfiability problem will be solved in a second. Gathering the correct binary sequence, and convert them to bytes will reveal the flag. 
