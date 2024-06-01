# What is Charm

Charm is a state management library.

```jsx

// counterCharm.ts 

const counterCharm = charm(0);

export const useCounter = useCharm(counterCharm);
export const inc = counterCharm.update(prev => prev + 1);


// Counter.tsx

const Counter = () => {
  const counter = useCounter();

  return <button onclick={inc}>
    clicked {counter} times
  </button>
}

```

# Derived atoms


```jsx

// word.ts 

const wordCharm = charm("compatibility");
const lettersCharm = derivedCharm(wordCharm, (word) => word.length, NeverSet)

export const useWord = useCharm(wordCharm);
export const setWord = charmSetter(wordCharm)

export const useLetters = useCharm(lettersCharm);


// Counter.tsx

const WordAndLetters = () => {
  const word = useWord();
  const letters = useLetters();

  return <>
    <p>
      <input onChange={e => setWord(e.target.value)}/>
    </p>
    <p>
      Word "{word}" contains {letters} letters.
    </p>
  </>    
}

```

## Split array atoms


```jsx
import { expect, mock, test } from "bun:test";
import { splitCharm } from "../src/arrays";
import { charm, type Charm } from "../src/charm";

test("charmList does not change when single value changes", () => {
  const arrayCharm = charm([10, 20]);
  const splitArrayCharm = splitCharm(arrayCharm);

  const charm0: Charm<number> = splitArrayCharm.get()[0];
  const charm1: Charm<number> = splitArrayCharm.get()[1];

  const nopFn = () => { }
  const subCharmMock = mock(nopFn as any)
  const sub0 = mock(nopFn as any)
  const sub1 = mock(nopFn as any)

  /// test


  splitArrayCharm.sub(subCharmMock)
  charm0.sub(sub0);
  charm1.sub(sub1);

  charm0.set(0);

  expect(sub0).toHaveBeenCalledTimes(1);

  expect(sub1).toHaveBeenCalledTimes(0);

  expect(subCharmMock).toHaveBeenCalledTimes(0);
});

```

# Using Provider for SSR

```jsx

const aCharm = charm(1);

const Comp = ({}) => {
  const value = useCharmValue(aCharm)
  return <button>
    {value}
  </button>
}


test("render two pages at the same time", () => {
  const page1 = execWithCharm(createCharmStore(), () => {
    const comp = <Comp />
    aCharm.set(10);
    return renderToString(comp)
  })
  const page2 = execWithCharm(createCharmStore(), () => {
    const comp = <Comp />
    aCharm.set(20);
    return renderToString(comp)
  })

  expect(page1).toEqual("<button>10</button>")
  expect(page2).toEqual("<button>20</button>")
});

```

# Install

```sh

npx jsr add @kaigorod/charm

# or

bunx jsr add @kaigorod/charm

```

This command will add the following line to your `package.json` file

```json
{
  //in package.json, 
  "@kaigorod/charm": "npm:@jsr/kaigorod__charm",
}
```


```
import { charm, charmGetter, charmSetter, useCharmValue } from "@kaigorod/charm";

const isImageSearchOnCharm = charm(false);

export const useIsImageSearchOn = () => useCharmValue(isImageSearchOnCharm);
export const setIsImageSearchOn = charmSetter(isImageSearchOnCharm);
```




# Inspiration
Charm is inspired by recoil and jotai state management libraries.

# Links

- Github Repo https://github.com/dmitrykaigorodov/charm
- Deno Package https://jsr.io/@kaigorod/charm
