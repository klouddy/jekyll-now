---
layout: post
title: Mockito Capture Verify Arguments passed to a mock
---

This post should explain how to use Mockito Capture to verigy arguments passed to a mock.  

# ArgumentCaptor

Use this when you want to verify the values of the arguments passed to a function in your Mocked Service.  Most of time I find myself using this for a function that has a sideeffect like persisting to a dabase or writing out to a file.  Normally I am doing the sideffect through a service.  So if I'm persisting the database I am using a jdbc or JPA service to do that.  The JDBC or JPA service is what I would mock and what I would be intereste in testing. This seperates me from the database but gives me a clear understanding as to what is being sent.

As an example lets say you have a `PostService` class.  You are setting that up as a mock and passing it the Class you are testing called `PostManager`.

```java
@Before
public void setup(){
  this.postService = Mockito.mock(PostService.class);
  this.postManager = new PostManger(this.postService);
}
```

In your test you are calling the method `PostManger.doSomethingToPost(postId)`.  This will do something to the post with this post id. Also inside of this function it will update the post in the database using `postService.save(post)`.  You can provide the value of the psot by using a `Mockito.when(this.postService.getPost(postId)).thenReturn(postIMockedUpEarlier)` as is typical of a mocked service. 

Finally you can validate the value of the object being passed to the `PostService.save()` method by using an `ArgumentCaptor`:

```java
ArgumentCaptor<Post> postSaveArgument = ArgumentCaptor.forClass(Post.class);
Mockito.verify(this.postService).save(postSaveArgument.capture());
Post myPostThatWasSaved = postSaveArgument.getValue();

// assert on myPostThatWasSaved
```

