# Graph Patterns #
1) > There are a total of numCourses courses you have to take, labeled from 0 to numCourses - 1. You are given an array prerequisites where prerequisites[i] = [ai, bi] indicates that you must take course bi first if you want to take course ai. < br/>
For example, the pair [0, 1], indicates that to take course 0 you have to first take course 1.<br/>
Return true if you can finish all courses. Otherwise, return false.<br/>

   ````js
   Input: numCourses = 2, prerequisites = [[1,0]]
   Output: true
   Explanation: There are a total of 2 courses to take. 
   To take course 1 you should have finished course 0. So it is possible.
   ````
   - Start by creating adjancency list of the graph
   - Create a map of all nodes and add all dependencies as list in the map.
   - Start loop on each key in map and recursively traverse each dependency of the current key.
   - If for any course there a re no dependency then return true as this course can be taken.
   - Maintain a visitedSet to track already visited nodes in a path.
   - If for a path a node reappears then return false as there is a cycle in graph and all courses cannot be taken
   - After traversing a path remove the course from visitedSet and set its dependency to empty so that we already know that this course can be taken.

   ````java
   public boolean canFinish(int numCourses, int[][] prerequisites) {
        
        if(prerequisites.length == 0){
            return true;
        }
        
        Map<Integer, List<Integer>> courceGraph = new HashMap<>();
        List<Integer> visited = new ArrayList<>();
        
        for(int[] course : prerequisites){
            
            List<Integer> insDeps = courceGraph.getOrDefault(course[0], new ArrayList<>());
            insDeps.add(course[1]);
            courceGraph.put(course[0], insDeps);
        }

        for(Integer course : courceGraph.keySet()){
            if(!canFinish(course, courceGraph, visited)) {
                return false;
            }
        }
        return true;
    }
    
    private boolean canFinish(Integer course, Map<Integer, List<Integer>> courceGraph, List<Integer> visited){
        
        if(courceGraph.get(course) == null){
            return true;
        }
        
        //If we come across a already visited course then graph has a cycle and should resturn false
        if(visited.contains(course)){
            return false;
        }
        
        visited.add(course);
        List<Integer> deps = courceGraph.get(course);
        
       for(Integer c : deps){
        	if(!canFinish(c, courceGraph, visited)) {
                return false;
            }
        }
        visited.remove(course);
        courceGraph.put(course, new ArrayList<>());
        return true;
    }
   ````
------------
