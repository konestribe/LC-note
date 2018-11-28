

### 1. BFS

## 1. calculate minimum step
- practise
- scenarios: we will be required to find the minimum step start from position st to ed
- main steps:
  - create queue, whenever we add new states onto the queue, we mark the states with visited 
  - each time we poll from queue, we check the spreading choice of current position. 
  - if the state is not visited and also valid, we add it to queue.
  - if we find one which is valid, we simply return
  
