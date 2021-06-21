// 2019-09-05

**1.g2o header files can not be found.(solved)**

**solution**: install `ros-kinetic-libg2o`  OR `sudo make install` in `g2o/build dir`.

// 2019-09-07

**1.cmake dependencies error. (solved)**

**solution**: check the real paths of `G2O_DIR` and `G2O_LIBRARIES` you wanna use, modify them in `CMakeCache.txt`.


//2019-09-09

**1.Make error: no matching function for call to  g2o::OptimizationAlgorithmLevenberg::OptimizationAlgorithm.....blabla [solved]**

**solution**: caused by old g2o version. modify `SFMgraph.cpp` at #36:

```
SFMgraph::SFMgraph() {
        typedef g2o::BlockSolverX BlockSolver;
        typedef g2o::LinearSolverCSparse<BlockSolver::PoseMatrixType> LinearSolver;
        //typedef g2o::LinearSolverPCG<BlockSolver::PoseMatrixType> LinearSolver;
        LinearSolver* solver = new LinearSolver();
        //BlockSolver* blockSolver = new BlockSolver(solver);
        BlockSolver* blockSolver = new BlockSolver(unique_ptr<BlockSolver::LinearSolverType>(solver)); // modified by Garry at 2019-09-10
        //g2o::OptimizationAlgorithmLevenberg* algorithm = new g2o::OptimizationAlgorithmLevenberg(blockSolver);
        g2o::OptimizationAlgorithmLevenberg* algorithm = new g2o::OptimizationAlgorithmLevenberg(unique_ptr<BlockSolver>(blockSolver)); // modified by Garry at 2019-09-10
```


**2.Make warnings:comparison between signed and unsigned integer expressions [solved]**

**solution**: modify the `int` to `std::size_t` in for loop comparison.

**3. undefined reference to `g2o::OptimizableGraph::addEdge(g2o::OptimizableGraph::Edge`

**solution**: caused by the conflict of g2o and ros-libg2o. **DO NOT** exe `sudo apt-get remove ros-xx-libg2o`, it may cause other problems.

Change the `G2O_XX_LIB` and `G2O_INCLUDE_DIR` in `/build/CMakeCache.txt`, for example:
```
G2O_INCLUDE_DIR: /home/garry/g2o/g2o
G2O_CORE_LIB:/home/garry/g2o/lib/libg2o_core.so
```

After modification, click "Configure" then "Generate";

OR modify FindG2O.cmake by adding `NO_DEFAULT_PATH` in `FIND_PATH/LIB`

At last, exe:

`cmake ..`

`make clean`

`make`

DONE.
