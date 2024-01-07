---
tags:
  - Refectoring
Completed: true
---
# 💡

Java에서 `interface.class`를 만들고 기반하여 구현 클래스를 작성할 수 있다는 걸 알고 있습니다. 그렇다면 Typescript에서 어떻게 *namespace*를 만들 수 있는걸까? 라는 의문을 가졌습니다.

Typescript 사용해 애플리케이션을 만들면서 *namespace*를 아무렇지도 않게 만들었지만, 근본적으로 어떻게 만드는지 궁금해졌습니다.
# Step

```ts title:queryShop.ts
export interface QueryShopFunctions {
  findAllShopByEmail: (params: { email: string }) => Promise<any>;
}
  
const queryShop: QueryShopFunctions = {
  findAllShopByEmail,
};
```

코드를 분석하면 이러합니다.

`findAllShopByEmail` 함수는 `interface`에서 정의된 함수로, `params: {email: string}` 형태의 객체로 구현 함수 매개변수를 `params`로 지정하게 합니다. 하지만 `queryShop` 함수가 만들어지기 전까지는 함수의 구현이 되지 않았습니다.

`queryShop`은 `QueryShopFunctions` 인터페이스를 따르는 객체이며, `findAllShopByEmail` 함수를 포함하고 있어야 합니다. 그리고 어떤 동작하는 지에 대한 정의가 있어야 하므로

```ts title:get
import axios from "axios";
import { QueryShopFunctions } from "../queryShop";

export const findAllShopByEmail: QueryShopFunctions["findAllShopByEmail"] =
  async (params) => {
    try {
      const response = await axios.get(
        "http://localhost:8080/api/shop/" + params.email
      );
      return response.data;
    } catch (error) {
      console.error(error);
      throw error;
    }
  };
```

다른 파일을 생성해서 구현하고, `queryShop.ts`으로 가져와서 부여합니다.
# 🎈

먼저 `queryShop`을 활용한 코드를 올립니다.

```ts title:HomeScreen
useEffect(() => {
    const fetchData = async () => {
      try {
        const shops: SHOP = await queryShop.findAllShopByEmail({
          email: "test@gmail.com",
        });
        console.log(shops);
      } catch (error) {
        console.log(error);
      }
    };
  
    fetchData();
  }, []);
```

위의 과정을 통해 코드를 분리하고, 보다 더 가독성이 있게 했습니다.
코드를 분석하면서 이런 원리로 동작한다는 것을 알았고 Typescript에서 `interface`의 역할에 대해서 조금 더 알 수 있게 되었습니다.