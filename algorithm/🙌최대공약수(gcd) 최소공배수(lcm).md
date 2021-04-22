# 🙌최대공약수(gcd) 최소공배수(lcm)

---

```javascript
function gcd(a, b){
	return !b ? a : gcd(b, a%b)
}
```

```javascript
function lcm(a, b){
	return a*b / gcd(a, b)
}
```

