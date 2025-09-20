Rewards can also come in several forms, let’s briefly consider a few major categories:

- **Sparse Rewards** - when feedback is rare or significant.
    - Think about the game of chess - which moves should be rewarded? The one that ended the game or the knight capture that happened 3 moves back? The ultimate goal of winning isn’t realized until the end, which is a **sparse** reward.
- **Dense Rewards -** when feedback is frequent and incremental.
    - Think about a robot moving closer to its target.
- **Shaped Rewards** - additional rewards introduced to guide the agent towards desired behavior by injecting some knowledge about the problem domain.
    - Think about chess, again. As we discussed, winning is a sparse reward. A **shaped** reward for this problem may be given for capturing pieces, providing intermediary feedback before the game is over.


Much like how we might accidentally teach a pet “tricks” we didn’t intend to teach them, we might’ve just taught the robot to _leap_ forward and fall over rather than walking. It took advantage of the reward function, but ultimately failed at the goal. That approach needs a bit more thought.

So, we revisit our rewards, and make them more robust. We might define more of the task with rewards and observations like these:

- The torso should be mostly vertical.
- The feet should contact the ground with a certain time threshold to not slide along the ground.
- The overall body tracks a certain heading to walk towards a target.
- Monitor a height sensor from the torso to the ground.
- We penalize the robot for putting the torso too low.

![[Pasted image 20250909192116.png]]
This is some of the magic of reinforcement learning: **we can define a goal, rather than the explicit steps to accomplish that goal** to teach a robot to do something new.

We’ll come back to this topic of reward engineering and task design. But first, where did this idea of reinforcement learning come from?