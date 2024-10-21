3 모듈성
===

## 3.1 자바스크립트에서의 모듈

- ui.js

```javascript
export function Button({text}) {
  // 버튼 컴포넌트
}

export function Header({title}) {
  // 헤더 컴포넌트
}
```

- page.js

```javascript
import { Header } from './ui.js';

function Page() {
  return <Header title="My Page"/>; 
}
```

- 한 컴포넌트에 모든 기능을 포함하고 있는 하나의 포스트 요소

```javascript
function Post({ post }) {
  return (
    <div>
      <img
        src={post.profileUrl}
        alt={`${post.author}'s profile`}
      />

      <h1>{post.title}</h1>

      <p>{post.text}</p>

      <div>Author: {post.author}</div>

      <div>Date: {post.date}</div>

      <p>{`${post.numLikes} likes`}</p>

      <p>{`${post.numComments} comments`}</p>

      <p>{`${post.numShares} shares`}</p>

      <button>Like</button>
      <button>Share</button>
      <button>Comment</button>
    </div>
  )
}

export default Post;
```

- `PostHeader` 컴포넌트

```javascript
function PostHeader({
  authorName,
  profileUrl,
  timestamp
}) {
  return (
    <div>
      <img
        src={profileUrl}
        alt={`${authorName}'s profile`}
      />
      <p>{authorName}</p>
      <p>{timestamp}</p>
    </div>
  );
}

export default PostHeader;
```

- `PostContent` 컴포넌트

```javascript
function PostContent({ text, media }) {
  return (
    <div>
      <p>{text}</p>
      {media.map((element, index) => (
        <img
          key={index}
          src={element.url}
          alt={element.alt}
        />
      ))}
    </div>
  );
}

export default PostContent
```

- `PostFooter` 컴포넌트

```javascript
function PostFooter({
  numLikes,
  numComments,
  numShares,
}) {
  return (
    <div>
      <p>{`${numLikes} likes`}</p>
      <p>{`${numComments} comments`}</p>
      <p>{`${numShares} shares`}</p>

      <button>Like</button>
      <button>Share</button>
      <button>Comment</button>
    </div>
  );
}

export default PostFooter
```

- 부모(상위) `Post` 컴포넌트

```javascript
function Post({
  author,
  content,
  footer,
}) {
  return (
    <div>
      <PostHeader
        authorName={author.name}
        profileUrl={author.profileUrl}
        timestamp={author.timestamp}
      />

      <PostContent
        text={content.text}
        media={content.media}
      />
      
      <PostFooter
        numLikes={footer.numLikes}
        numComments={footer.numComments}
        numShares={footer.numShares}
      />
    </div>
  );
}

export default Post
```

## 3.2 지연 로딩

- `Post` 컴포넌트 정적 임포트

```javascript
import React from 'react';

// Post 컴포넌트 임포트
import Post from './components/Post';

function App() {
  return (
    <div>
      <Post />
    </div> 
  );
}

export default App;
```

- `Post` 컴포넌트 동적 임포트

```javascript
import React, { lazy, Suspense } from 'react';

// Post 컴포넌트 동적 임포트
const Post = lazy(
  () => import("./components/Post"),
);

function App() {
  return (
    <div>
      {/* 
        Suspense를 사용해 Post가 동적으로 로딩되는 동안 대체 컴포넌트를 렌더링한다 
      */}
      <Suspense fallback={<div>Loading...</div>}>
        <Post />
      </Suspense>
    </div>
  );
}

export default App;
```

- 버튼 클릭 시 `Post` 컴포넌트를 지연 로딩하기

```javascript
import React, { useState } from "react";

function App() {
  const [Post, setPost] = useState(null);

  const handleClick = () => {
    import("./components/Post").then((module) => {
      setPost(() => module.default);
    });
  };

  return (
    <div>
      {Post ? (
        <Post />
      ) : (
        <button onClick={handleClick}>
          Load Post
        </button>
      )}
    </div>
  );
}

export default App;
```

- Intersection Observer를 사용한 지연 로딩

```javascript
import React, {
  useState,
  useRef,
  lazy,
  Suspense,
} from "react";
import useIntersectionObserver from "./hooks";

const Post = lazy(
  () => import("./components/Post"),
);

function App() {
  const [shouldRenderPost, setShouldRenderPost] =
    useState(false);
  const postRef = useRef(null);

  const handleIntersect = ([entry]) => {
    if (entry.isIntersecting) {
      setShouldRenderPost(true);
    }
  };
  useIntersectionObserver(
    postRef,
    handleIntersect,
    { threshold: 0 },
  );

  return (
    <div>
      <div style={{ height: "1000px" }}>
        Some content before the post
      </div>
      <div ref={postRef}>
        {shouldRenderPost ? (
          <Suspense
            fallback={<div>Loading...</div>}
          >
            <Post />
          </Suspense>
        ) : (
          <div>Loading...</div>
        )}
      </div>
      <div style={{ height: "1000px" }}>
        Some content after the post
      </div>
    </div>
  );
}

export default App;
```
